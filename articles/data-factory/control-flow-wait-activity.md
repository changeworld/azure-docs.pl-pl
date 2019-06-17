---
title: Działanie usługi Azure Data Factory wait | Dokumentacja firmy Microsoft
description: Działanie Wait wstrzymuje wykonywanie potoku w wybranym okresie.
services: data-factory
documentationcenter: ''
author: shlo
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: shlo
ms.openlocfilehash: 66d79bc1597cd8f3c7e01eb8227eb7c91ba04d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764755"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Wykonaj działania wait w usłudze Azure Data Factory
Gdy używasz działania Wait w potoku, potok czeka przez określony okres z kontynuowaniem wykonywania kolejnych działań. 

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
name | Nazwa `Wait` działania. | String | Tak
type | Musi być równa **oczekiwania**. | String | Tak
waitTimeInSeconds | Liczba sekund, przez potok czeka przed kontynuowaniem przetwarzania. | Integer | Yes

## <a name="example"></a>Przykład

> [!NOTE]
> Ta sekcja zawiera definicje JSON i przykładowe polecenia programu PowerShell, aby uruchomić potok. Aby uzyskać wskazówki krok po kroku instrukcje tworzenia potoku usługi Data Factory przy użyciu definicji JSON i programu Azure PowerShell, zobacz [samouczek: tworzenie fabryki danych przy użyciu programu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Potok z działaniem oczekiwania
W tym przykładzie potok zawiera dwa działania: **Do momentu** i **oczekiwania**. Działanie oczekiwania jest skonfigurowany do poczekaj 1 sekundy. Potok jest uruchamiany działanie internetowe w pętli za pomocą jednej sekundy czas między poszczególnymi uruchomieniami oczekiwania. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Kolejne kroki
Zobacz inne działania przepływu sterowania obsługiwanych przez usługę Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
