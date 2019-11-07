---
title: Działanie oczekiwania w Azure Data Factory
description: Działanie oczekiwania wstrzymuje wykonywanie potoku w określonym przedziale czasu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: f9dd53fded06eec169219d00993620a0f2aa2bf0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678241"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Działanie wykonywania oczekiwania w Azure Data Factory
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

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa działania `Wait`. | Ciąg | Tak
type | Musi być ustawiony na wartość **czekaj**. | Ciąg | Tak
waitTimeInSeconds | Liczba sekund oczekiwania przez potok przed kontynuowaniem przetwarzania. | Liczba całkowita | Tak

## <a name="example"></a>Przykład

> [!NOTE]
> Ta sekcja zawiera definicje JSON i przykładowe polecenia programu PowerShell umożliwiające uruchomienie potoku. Aby zapoznać się z instrukcjami krok po kroku dotyczącymi tworzenia potoku Data Factory przy użyciu definicji Azure PowerShell i JSON, zobacz [Samouczek: Tworzenie fabryki danych przy użyciu Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Potok z działaniem oczekiwania
W tym przykładzie potok ma dwie działania: **do** **czekania i oczekiwania**. Działanie oczekiwania jest skonfigurowane tak, aby czekało na sekundę. Potok uruchamia działanie sieci Web w pętli z upływem jednego sekundy czasu oczekiwania między każdym przebiegiem. 

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

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez Data Factory: 

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
