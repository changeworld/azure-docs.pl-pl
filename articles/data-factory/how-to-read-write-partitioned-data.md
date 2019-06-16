---
title: Jak Odczyt lub zapis partycjonowania danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Odczyt lub zapis danych partycjonowanych w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: f2d780900a0cd24f2d70499573a4055dc836ae0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466990"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Jak Odczyt lub zapis partycjonowania danych w usłudze Azure Data Factory

W usłudze Azure Data Factory w wersji 1 można odczytu lub zapisu danych podzielonych na partycje za pomocą **SliceStart**, **SliceEnd**, **WindowStart**, i **WindowEnd** zmiennych systemowych. W bieżącej wersji usługi fabryka danych to zachowanie można osiągnąć za pomocą parametru potoku i wyzwalacza godziny rozpoczęcia lub zaplanowanego czasu jako wartość parametru. 

## <a name="use-a-pipeline-parameter"></a>Parametr potoku 

W usłudze Data Factory w wersji 1, można użyć **partitionedBy** właściwości i **SliceStart** zmiennej systemowej, jak pokazano w poniższym przykładzie: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Aby uzyskać więcej informacji na temat **partitonedBy** właściwości, zobacz [kopiowanie danych do i z usługi Azure Blob storage za pomocą usługi Azure Data Factory](v1/data-factory-azure-blob-connector.md#dataset-properties). 

Aby uzyskać takie zachowanie w bieżącej wersji usługi fabryka danych: 

1. Zdefiniuj *potoku parametr* typu **ciąg**. W poniższym przykładzie jest nazwa parametru potoku **windowStartTime**. 
2. Ustaw **folderPath** w definicji zestawu danych, aby odwoływać się do wartości parametrów potoku. 
3. Kiedy wywołujesz potoku na żądanie, należy przekazać rzeczywistej wartości parametru. Można również przekazać wyzwalacza godziny rozpoczęcia lub zaplanowanym terminie dynamicznie w czasie wykonywania. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Przekazywanie wartości przy użyciu wyzwalacza

W poniższej definicji wyzwalacza okna wirowania, godzina rozpoczęcia okna wyzwalacza jest przekazywany jako wartość parametru potoku **windowStartTime**: 

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

Oto przykładowa Definicja zestawu danych:

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

Aby uzyskać szczegółowy przewodnik, jak utworzyć fabrykę danych, która zawiera potok, zobacz [Szybki Start: Tworzenie fabryki danych](quickstart-create-data-factory-powershell.md). 

