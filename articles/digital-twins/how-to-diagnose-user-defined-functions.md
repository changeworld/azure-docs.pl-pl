---
title: Jak można debugować funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące debugowania funkcji UDF w reprezentacji urządzeń cyfrowych platformy Azure
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: stefanmsft
ms.openlocfilehash: 852b2d35ae605f5529d162d52655fd258ca07c5a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946100"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Jak debugować problemy związane z funkcjami zdefiniowanymi przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure

Ten artykuł zawiera podsumowanie sposób diagnozowania funkcje zdefiniowane przez użytkownika. Następnie identyfikuje niektóre z najbardziej typowych scenariuszy podczas pracy z nimi.

## <a name="debug-issues"></a>Debugowanie błędów

Wiedza, jak diagnozować problemy, które powstają w ramach wystąpienia Twins cyfrowych platformy Azure pomoże zidentyfikować problem, przyczyna problemu i rozwiązanie.

### <a name="enable-log-analytics-for-your-instance"></a>Włączanie analizy dzienników dla wystąpienia usługi

Dzienniki i metryki dla wystąpienia Twins cyfrowych platformy Azure są udostępniane za pośrednictwem usługi Azure Monitor. Poniższa dokumentacja przyjęto założenie, utworzono [usługi Azure Log Analytics](../log-analytics/log-analytics-queries.md) obszar roboczy za pomocą [witryny Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)za pośrednictwem [wiersza polecenia platformy Azure](../log-analytics/log-analytics-quick-create-workspace-cli.md), lub za pomocą [ Program PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md).

> [!NOTE]
> Może wystąpić opóźnienie 5 minut, podczas wysyłania zdarzeń do **usługi Log Analytics** po raz pierwszy.

Przeczytaj artykuł ["Zbieranie i używanie dane dzienników z zasobów platformy Azure"](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) na włączanie ustawień diagnostycznych dla swojego wystąpienia Twins cyfrowych platformy Azure za pośrednictwem portalu, wiersza polecenia platformy Azure lub programu PowerShell. Upewnij się, że wybrano wszystkie kategorie dzienników, metryk i obszaru roboczego usługi Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Telemetria śledzenia usługi czujnika

Upewnij się, że ustawienia diagnostyczne są włączone w ramach wystąpienia Twins cyfrowych platformy Azure, są zaznaczone wszystkie kategorie dziennika i czy dzienniki są wysyłane do usługi Azure Log Analytics.

Aby dopasować komunikaty telemetryczne czujnik, jego odpowiednich dzienniki, można określić identyfikator korelacji na dane zdarzenia są wysyłane. Aby to zrobić, należy ustawić `x-ms-client-request-id` właściwość identyfikatora GUID.

Po wysłaniu danych telemetrycznych, otwiera się usługi Azure Log Analytics na zapytanie o dzienniki przy użyciu zestawu identyfikator korelacji:

```Kusto
AzureDiagnostics
| where CorrelationId = 'yourCorrelationIdentifier'
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourCorrelationIdentifier* | Identyfikator korelacji, który został określony na dane zdarzeń |

Jeśli zalogujesz się funkcji zdefiniowanych przez użytkownika, tych dzienników będą wyświetlane w wystąpieniu usługi Azure Log Analytics z kategorią `UserDefinedFunction`. Aby je pobrać, wprowadź następujące warunki zapytania w usłudze Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

Aby uzyskać więcej informacji o operacjach zaawansowanych zapytań, zobacz [wprowadzenie do zapytań](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="identify-common-issues"></a>Zidentyfikować typowe problemy

Diagnozowanie i identyfikuje typowe problemy są ważne podczas rozwiązywania problemów z rozwiązania. Kilka typowych problemów napotykanych podczas opracowywania funkcje zdefiniowane przez użytkownika zestawiono poniżej.

### <a name="ensure-a-role-assignment-was-created"></a>Upewnij się, że utworzono przypisania roli

Bez przypisania roli, utworzone w ramach interfejsu API zarządzania funkcja zdefiniowana przez użytkownika nie będą dostępne wykonuje wszystkie działania, takie jak wysyłanie powiadomień, pobierania metadanych, i ustawienie obliczonych wartości w ramach topologii.

Sprawdź, czy przydział roli istnieje funkcji zdefiniowanych przez użytkownika za pomocą usługi API Management:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/roleassignments?path=/&traverse=Down&objectId=yourUserDefinedFunctionId
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourUserDefinedFunctionId* | Identyfikator funkcji zdefiniowanej przez użytkownika do pobrania przypisań ról|

Jeśli nie przypisania roli są pobierane, postępuj zgodnie z tego artykułu na [jak można utworzyć przypisania roli, funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Sprawdź, jeśli dopasowywania będzie działać w przypadku dane telemetryczne z czujników

Przy użyciu następującego wywołania interfejsu API zarządzania wystąpieniami danego Twins cyfrowych platformy Azure można określić, czy dany dopasowywania ma zastosowanie do danego czujnika.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/matchers/yourMatcherIdentifier/evaluate/yourSensorIdentifier?enableLogging=true
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourMatcherIdentifier* | Identyfikator dopasowywania, którą chcesz ocenić |
| *yourSensorIdentifier* | Identyfikator czujnik, który chcesz ocenić |

Odpowiedź:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>Sprawdź, co spowoduje czujnika

Przy użyciu następującego wywołania interfejsu API zarządzania wystąpieniami danego Twins cyfrowych platformy Azure można określić identyfikatory funkcji zdefiniowanych przez użytkownika, które będą wyzwalane przez dany czujnik przychodzących danych telemetrycznych:

```plaintext
GET https://yourManagementApiUrl/api/v1.0/sensors/yourSensorIdentifier/matchers?includes=UserDefinedFunctions
```

| Nazwa atrybutu niestandardowego | Zamień |
| --- | --- |
| *yourManagementApiUrl* | Pełna ścieżka adresu URL dla interfejsu API zarządzania  |
| *yourSensorIdentifier* | Identyfikator czujnik, który będzie wysyłać dane telemetryczne |

Odpowiedź:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problem z otrzymywania powiadomień

Gdy nie odbieranie powiadomień z poziomu wyzwalane funkcji zdefiniowanej przez użytkownika, upewnij się, że Twój parametr typu obiektu topologii jest zgodny z typem identyfikatora, który jest używany.

**Niepoprawne** przykładu:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

W tym scenariuszu pojawia się, ponieważ identyfikator używany odwołuje się do czujnika, gdy określony typ obiektu topologia jest "Obszar".

**Poprawne** przykładu:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Najprostszym sposobem, aby nie uruchamiać ten problem występuje, jest użycie `Notify` metody na obiekt metadanych.

Przykład:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Typowe wyjątki dotyczące diagnostyki

Po włączeniu ustawienia diagnostyczne, mogą wystąpić następujące typowe wyjątki dotyczące:

1. **Ograniczanie**: Jeśli funkcji zdefiniowanych przez użytkownika przekracza szybkość wykonywania limity opisane w [limity](./concepts-service-limits.md) artykułu, zostanie ograniczona. Ograniczanie pociąga za sobą nie dalszych operacji pomyślnym wykonaniu, dopóki nie wygasną limitów.

1. **Nie można odnaleźć danych**: Jeśli funkcji zdefiniowanych przez użytkownika prób dostępu do metadanych, który nie istnieje, operacja zakończy się niepowodzeniem.

1. **Nieautoryzowane**: Jeśli nie ma zdefiniowanej przez użytkownika funkcji przypisania roli ustawiona lub nie ma wystarczające uprawnienia dostępu do niektórych metadanych w topologii, operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak włączyć [monitorowania i dzienniki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) w reprezentacji urządzeń cyfrowych platformy Azure.
