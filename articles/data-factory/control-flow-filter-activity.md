---
title: Filtrowanie aktywności w fabryce danych platformy Azure
description: Działanie Filtr filtruje dane wejściowe.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/04/2018
ms.openlocfilehash: 5794a2c754e2082fdf45ad689bdb89ea494541c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679856"
---
# <a name="filter-activity-in-azure-data-factory"></a>Filtrowanie aktywności w fabryce danych platformy Azure
Za pomocą działania filtru w potoku można zastosować wyrażenie filtru do tablicy wejściowej. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyFilterActivity",
    "type": "filter",
    "typeProperties": {
        "condition": "<condition>",
        "items": "<input array>"
    }
}
```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa `Filter` działania. | Ciąg | Tak
type | Musi być ustawiony na **filtrowanie**. | Ciąg | Tak
Warunek | Warunek, który ma być używany do filtrowania danych wejściowych. | Wyrażenie | Tak
Elementy | Tablica wejściowa, na której należy zastosować filtr. | Wyrażenie | Tak

## <a name="example"></a>Przykład

W tym przykładzie potok ma dwa działania: **Filter** i **ForEach**. Działanie Filtr jest skonfigurowane do filtrowania tablicy wejściowej dla elementów o wartości większej niż 3. ForEach działania następnie iteruje nad filtrowane wartości i ustawia **test** zmiennej do bieżącej wartości.

```json
{
    "name": "PipelineName",
    "properties": {
        "activities": [{
                "name": "MyFilterActivity",
                "type": "filter",
                "typeProperties": {
                    "condition": "@greater(item(),3)",
                    "items": "@pipeline().parameters.inputs"
                }
            },
            {
            "name": "MyForEach",
            "type": "ForEach",
            "dependsOn": [
                {
                    "activity": "MyFilterActivity",
                    "dependencyConditions": [
                        "Succeeded"
                    ]
                }
            ],
            "userProperties": [],
            "typeProperties": {
                "items": {
                    "value": "@activity('MyFilterActivity').output.value",
                    "type": "Expression"
                },
                "isSequential": "false",
                "batchCount": 1,
                "activities": [
                    {
                        "name": "Set Variable1",
                        "type": "SetVariable",
                        "dependsOn": [],
                        "userProperties": [],
                        "typeProperties": {
                            "variableName": "test",
                            "value": {
                                "value": "@string(item())",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        }],
        "parameters": {
            "inputs": {
                "type": "Array",
                "defaultValue": [1, 2, 3, 4, 5, 6]
            }
        },
        "variables": {
            "test": {
                "type": "String"
            }
        },
        "annotations": []
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Uzyskaj aktywność metadanych](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
