---
title: Schemat zdarzeń dziennika automatycznej skali platformy Azure
description: Format dzienników do monitorowania i rozwiązywania problemów z akcjami skalowania automatycznego
ms.topic: conceptual
ms.date: 11/14/2019
ms.subservice: autoscale
ms.openlocfilehash: 3c32f15208a8e692054ee6c1f7effc6b7c89de3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75395943"
---
# <a name="azure-monitor-autoscale-actions-resource-log-schema"></a>Schemat dziennika zasobów akcji automatycznego skalowania akcji usługi Azure Monitor

Poniżej przedstawiono ogólne formaty dzienników zasobów skalowania automatycznego z dołączonymi przykładowymi danymi. Nie wszystkie poniższe przykłady są poprawnie utworzone JSON, ponieważ mogą zawierać wiele wartości, które mogą być prawidłowe dla danego pola. 

Użyj zdarzeń tego typu, aby rozwiązać problemy, które mogą mieć z skalowania automatycznego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów ze skalowaniem automatycznym](autoscale-troubleshoot.md).


## <a name="profile-evaluation"></a>Ocena profilu

Nagrywane, gdy skalowanie automatyczne po raz pierwszy patrzy na profil skalowania automatycznego

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": ["FixedDateProfileEvaluation", "RecurrentProfileEvaluation", "DefaultProfileEvaluation"],
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "profile": "defaultProfile",
    "profileSelected": [true, false]
  }
}
```

## <a name="profile-cooldown-evaluation"></a>Ocena odnowienia profilu

Rejestrowane, gdy skalowanie automatyczne ocenia, czy nie należy wykonywać skali z powodu okresu ochłodzenia. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleCooldownEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "selectedProfile": "defaultProfile",
    "scaleDirection": ["Increase", "Decrease"]
    "lastScaleActionTime": "2018-09-10 18:08:00.6132593",
    "cooldown": "00:30:00",
    "evaluationTime": "2018-09-10 18:11:00.6132593",
    "skipRuleEvaluationForCooldown": true
  }
}
```

## <a name="rule-evaluation"></a>Ocena reguł

Rejestrowane, gdy skalowanie automatyczne po raz pierwszy rozpoczyna ocenę określonej reguły skali. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "timeWindow": "00:10:00",
    "timeAggregationType": "Average",
    "operator": "GreaterThan",
    "threshold": 70,
    "observedValue": 25,
    "estimateScaleResult": ["Triggered", "NotTriggered", "Unknown"]
  }
}
```

## <a name="metric-evaluation"></a>Ocena metryczna

Rejestrowane podczas skalowania automatycznego oceny metryki używanej do wyzwalania akcji skalowania. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "MetricEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "startTime": "2018-09-10 18:00:00.43833793",
    "endTime": "2018-09-10 18:10:00.43833793",
    "data": [0.33333333333333331,0.16666666666666666,1.0,0.33333333333333331,2.0,0.16666666666666666,9.5]
  }
}
```

## <a name="instance-count-evaluation"></a>Ocena liczby wystąpień

Rejestrowane, gdy skalowanie automatyczne ocenia liczbę wystąpień już uruchomionych w ramach przygotowań do podjęcia decyzji, czy należy uruchomić więcej, zamknąć niektóre lub nic nie robić. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceCountEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "minimumInstanceCount": 15,
    "maximumInstanceCount": 30,
    "defaultInstanceCount": 20
  }
}
```

## <a name="scale-action-evaluation"></a>Ocena działania skali

Rejestrowane, gdy skalowanie automatyczne rozpoczyna ocenę, jeśli ma nastąpić akcja skalowania. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleActionOperationEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "lastScaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "lastScaleActionOperationStatus": ["InProgress", "Timeout"]
    "skipCurrentAutoscaleEvaluation": [true, false]
  }
}
```

## <a name="instance-update-evaluation"></a>Ocena aktualizacji wystąpienia

Rejestrowane, gdy skalowanie automatyczne aktualizuje liczbę uruchomionych wystąpień obliczeniowych w górę lub w dół.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceUpdateEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "shouldUpdateInstance": [true, false],
    "reason": ["Scale down action triggered", "Scale up to default instance count", ...]
  }
}
```

## <a name="scale-action"></a>Akcja skalowania

Rejestrowane, gdy skalowanie automatyczne inicjuje akcję skalowania w górę lub w dół. 
```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "resultType": ["Succeeded", "InProgress", "Failed"],
  "resultDescription": ["Create async operation job failed", ...]
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "scaleDirection": ["Increase", "Decrease"],
    ["createdAsyncScaleActionJob": [true, false],]
    ["createdAsyncScaleActionJobId": "378ejr-7yye-892d-17dd-92ndijfe1738",]
  }
}
```

## <a name="scale-action-tracking"></a>Śledzenie akcji skalowania

Rejestrowane w różnych odstępach akcji skalowania wystąpienia.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "scaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "scaleActionOperationStatus": ["InProgress", "Timeout", "Canceled", ...],
    "scaleActionMessage": ["Scale action is inprogress", ...]
  }
}
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [skalowaniu automatycznym](autoscale-overview.md)
