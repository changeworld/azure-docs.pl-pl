---
title: Jak do odczytu lub zapisu na partycje danych w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odczytu lub zapisu danych podzielonej na partycje w fabryce danych Azure.
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
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: 59644f3318e2bf9c4f0ea6c3f5699fe1d19f2089
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053714"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Jak do odczytu lub zapisu na partycje danych w fabryce danych Azure
W wersji 1 usługi fabryka danych Azure obsługiwane odczytu lub zapisu danych podzielonej na partycje przy użyciu SliceStart/SliceEnd/WindowStart/WindowEnd zmienne systemowe. W bieżącej wersji fabryki danych to zachowanie można osiągnąć za pomocą parametru potoku i czas/zaplanowana godzina rozpoczęcia tego wyzwalacza jako wartość parametru. 

## <a name="use-a-pipeline-parameter"></a>Użyj parametru potoku 
W wersji 1 można użyć właściwości partitionedBy i SliceStart zmiennej systemowej, jak pokazano w poniższym przykładzie: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Aby uzyskać więcej informacji na temat właściwości partitonedBy, zobacz [łącznika obiektów Blob platformy Azure w wersji 1](v1/data-factory-azure-blob-connector.md#dataset-properties) artykułu. 

W bieżącej wersji fabryki danych oraz sposobów osiągnięcia tego zachowania jest wykonywanie następujących czynności: 

1. Zdefiniuj **potoku parametru** typu String. W poniższym przykładzie nazwa parametru potoku jest **windowStartTime**. 
2. Ustaw **folderPath** w definicji zestawu danych, aby odwołać wartość parametru potoku. 
3. Przekaż rzeczywista wartość dla parametru podczas wywoływania procesu na żądanie, lub Przekaż czas/zaplanowana godzina rozpoczęcia tego wyzwalacza dynamicznie w czasie wykonywania. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Podaj wartość od wyzwalacza
W następujących definicji wyzwalacza okno wirowania, godzina rozpoczęcia okna wyzwalacza jest przekazywany jako wartość parametru potoku **windowStartTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Przykład

Oto przykład definicji zestawu danych:

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Definicja potoku: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać pełny Przewodnik tworzenia fabryka danych z potokiem, zobacz [Szybki Start: tworzenie fabryki danych](quickstart-create-data-factory-powershell.md). 
