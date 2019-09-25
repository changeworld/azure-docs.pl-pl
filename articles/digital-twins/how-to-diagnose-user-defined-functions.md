---
title: Jak debugować UDF w usłudze Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Wskazówki dotyczące debugowania UDF w usłudze Azure Digital bliźniaczych reprezentacji.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: c1bd33ea5cbe45d6ff862645d614d54d20110ef4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260859"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak debugować funkcje zdefiniowane przez użytkownika w usłudze Azure Digital bliźniaczych reprezentacji

W tym artykule opisano sposób diagnozowania i debugowania funkcji zdefiniowanych przez użytkownika w usłudze Azure Digital bliźniaczych reprezentacji. Następnie identyfikuje kilka typowych scenariuszy znalezionych podczas debugowania.

>[!TIP]
> Przeczytaj [temat jak skonfigurować monitorowanie i rejestrowanie](./how-to-configure-monitoring.md) , aby dowiedzieć się więcej o konfigurowaniu narzędzi do debugowania w usłudze Azure Digital bliźniaczych reprezentacji przy użyciu dzienników aktywności, dzienników diagnostycznych i Azure monitor.

## <a name="debug-issues"></a>Problemy z debugowaniem

Wiedząc, jak zdiagnozować problemy w usłudze Azure Digital bliźniaczych reprezentacji, można skutecznie analizować problemy, identyfikować przyczyny problemów i udostępniać odpowiednie rozwiązania.

Do tego celu są dostępne różne narzędzia do rejestrowania, analizy i diagnostyki.

### <a name="enable-logging-for-your-instance"></a>Włącz rejestrowanie dla swojego wystąpienia

Usługa Azure Digital bliźniaczych reprezentacji obsługuje niezawodne rejestrowanie, monitorowanie i analizę. Deweloperzy rozwiązań mogą korzystać z dzienników Azure Monitor, dzienników diagnostycznych, dzienników aktywności i innych usług w celu obsługi złożonych potrzeb związanych z monitorowaniem aplikacji IoT. Opcje rejestrowania można łączyć z kwerendą lub wyświetlać rekordy w kilku usługach i zapewnić szczegółowe pokrycie rejestrowania w wielu usługach.

* Aby uzyskać konfigurację rejestrowania specyficzną dla usługi Azure Digital bliźniaczych reprezentacji, przeczytaj artykuł [jak skonfigurować monitorowanie i rejestrowanie](./how-to-configure-monitoring.md).
* Zapoznaj się z omówieniem [Azure monitor](../azure-monitor/overview.md) , aby dowiedzieć się więcej o zaawansowanych ustawieniach rejestrowania włączonych za pomocą Azure monitor.
* Zapoznaj się z artykułem [zbieranie danych dzienników z zasobów platformy Azure i korzystanie z nich](../azure-monitor/platform/resource-logs-overview.md) w celu konfigurowania ustawień dziennika diagnostycznego w usłudze Azure Digital bliźniaczych reprezentacji za pomocą witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

Po skonfigurowaniu można wybrać wszystkie kategorie i metryki dzienników oraz zaawansowane Azure Monitor obszary robocze usługi log Analytics do obsługi działań związanych z debugowaniem.

### <a name="trace-sensor-telemetry"></a>Dane telemetryczne czujnika śledzenia

Aby śledzić dane telemetryczne czujnika, sprawdź, czy ustawienia diagnostyczne są włączone dla Twojego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Następnie upewnij się, że wybrano wszystkie żądane kategorie dzienników. Na koniec upewnij się, że żądane dzienniki są wysyłane do dzienników Azure Monitor.

Aby dopasować komunikat telemetrii czujnika do odpowiednich dzienników, można określić identyfikator korelacji dla wysyłanych danych zdarzenia. Aby to zrobić, należy ustawić `x-ms-client-request-id` właściwość na identyfikator GUID.

Po wysłaniu telemetrii Otwórz Azure Monitor Log Analytics, aby wykonać zapytanie dotyczące dzienników przy użyciu zestawu identyfikator korelacji:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Wartość zapytania | Zamień na |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Identyfikator korelacji określony dla danych zdarzenia |

Jeśli włączysz rejestrowanie dla funkcji zdefiniowanej przez użytkownika, te dzienniki będą widoczne w wystąpieniu usługi log Analytics z kategorią `UserDefinedFunction`. Aby je pobrać, wprowadź następujący warunek zapytania w usłudze log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Aby uzyskać więcej informacji na temat zaawansowanych operacji związanych z zapytaniami, przeczytaj artykuł [wprowadzenie do zapytań](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identyfikowanie typowych problemów

Diagnozowanie i identyfikowanie typowych problemów jest ważne podczas rozwiązywania problemów z rozwiązaniem. Niektóre często występujące problemy podczas tworzenia funkcji zdefiniowanych przez użytkownika są podsumowywane w poniższych podsekcjach.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Sprawdź, czy utworzono przypisanie roli

Bez przypisania roli utworzonego w ramach interfejsu API zarządzania funkcja zdefiniowana przez użytkownika nie ma dostępu do wykonywania żadnych akcji, takich jak wysyłanie powiadomień, Pobieranie metadanych i ustawianie wartości obliczanych w topologii.

Sprawdź, czy przypisanie roli istnieje dla funkcji zdefiniowanej przez użytkownika za pomocą interfejsu API zarządzania:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Wartość parametru | Zamień na |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika do pobierania przypisań ról dla|

Dowiedz się, [jak utworzyć przypisanie roli dla funkcji zdefiniowanej przez użytkownika](./how-to-user-defined-functions.md), jeśli nie istnieją żadne przypisania ról.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Sprawdź, czy odpowiednika działa dla danych telemetrycznych czujnika

Za pomocą następującego wywołania interfejsu API zarządzania wystąpieniami Digital bliźniaczych reprezentacji platformy Azure można określić, czy dany odpowiednik ma zastosowanie do danego czujnika.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Identyfikator dopasowań, który chcesz oszacować |
| *YOUR_SENSOR_IDENTIFIER* | Identyfikator czujnika, który chcesz oszacować |

Odpowiedź:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Sprawdź, jakie są wyzwalacze czujnika

Korzystając z następującego wywołania interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management, można określić identyfikatory funkcji zdefiniowanych przez użytkownika wyzwalane przez dane telemetrii przychodzącej czujnika:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Identyfikator czujnika do wysyłania telemetrii |

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

### <a name="issue-with-receiving-notifications"></a>Problem z otrzymywaniem powiadomień

Gdy nie otrzymujesz powiadomień z wywołanej funkcji zdefiniowanej przez użytkownika, upewnij się, że parametr typu obiektu topologii jest zgodny z typem używanego identyfikatora.

**Nieprawidłowe** Przyklad

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Ten scenariusz występuje, ponieważ użyty identyfikator odwołuje się do czujnika, a określony typ obiektu topologii `Space`to.

**Poprawne** Przyklad

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Najprostszym sposobem nieuruchomienia tego problemu jest użycie `Notify` metody w obiekcie metadanych.

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

## <a name="common-diagnostic-exceptions"></a>Typowe wyjątki diagnostyki

W przypadku włączenia ustawień diagnostycznych mogą wystąpić następujące typowe wyjątki:

1. **Ograniczanie przepustowości**: Jeśli funkcja zdefiniowana przez użytkownika przekracza limity szybkości wykonywania opisane w artykule [limity usług](./concepts-service-limits.md) , zostanie ono ograniczone. Kolejne operacje nie są wykonywane pomyślnie do momentu wygaśnięcia limitów ograniczania.

1. **Nie znaleziono danych**: Jeśli zdefiniowana przez użytkownika funkcja próbuje uzyskać dostęp do nieistniejących metadanych, operacja kończy się niepowodzeniem.

1. **Nie autoryzowane**: Jeśli zdefiniowana przez użytkownika funkcja nie ma ustawionego przypisania roli lub nie ma wystarczających uprawnień dostępu do określonych metadanych z topologii, operacja kończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak włączyć [monitorowanie i dzienniki](./how-to-configure-monitoring.md) w usłudze Azure Digital bliźniaczych reprezentacji.

- Zapoznaj się z artykułem [Omówienie dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md) , aby uzyskać więcej opcji rejestrowania na platformie Azure.
