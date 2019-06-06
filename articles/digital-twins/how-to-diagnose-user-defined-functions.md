---
title: Jak można debugować funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące debugowania funkcji UDF w reprezentacji urządzeń cyfrowych platformy Azure.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: 455e78c63960103f5facae764aff3d2b3b2a590d
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735201"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak można debugować funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure

Ten artykuł zawiera podsumowanie jak diagnozować i debugować funkcje zdefiniowane przez użytkownika w reprezentacji urządzeń cyfrowych platformy Azure. Następnie identyfikuje niektóre z najbardziej typowych scenariuszy podczas debugowania ich.

>[!TIP]
> Odczyt [jak skonfigurować monitorowanie i rejestrowanie](./how-to-configure-monitoring.md) Aby dowiedzieć się więcej o konfigurowaniu narzędzia debugowania w cyfrowych bliźniaczych reprezentacji platformy Azure przy użyciu dzienników aktywności, dzienniki diagnostyczne i usługi Azure Monitor.

## <a name="debug-issues"></a>Debugowanie błędów

Wiedza, jak diagnozować problemy w reprezentacji urządzeń cyfrowych platformy Azure pozwala na efektywne analizowania problemów ustalenie przyczyn tych problemów i zapewnia odpowiednie rozwiązania dla nich.

Szereg rejestrowania, Analityki i narzędzi diagnostycznych znajdują się w tym celu.

### <a name="enable-logging-for-your-instance"></a>Włącz rejestrowanie dla swojego wystąpienia

Twins cyfrowych platformy Azure obsługuje niezawodne rejestrowanie, monitorowanie i analizy. Rozwiązania deweloperzy mogą używać usługi Azure Monitor dzienników, dzienniki diagnostyczne, dzienniki aktywności i innych usług, do obsługi złożonych potrzeb monitorowania aplikacji IoT. Opcje rejestrowania można łączyć z zapytania lub wyświetla rekordy w kilku usługach oraz zapewniają szczegółowe rejestrowanie pokrycie dla wielu usług.

* Konfiguracja rejestrowania specyficzne dla bliźniaczych reprezentacji cyfrowych platformy Azure, można znaleźć [jak skonfigurować monitorowanie i rejestrowanie](./how-to-configure-monitoring.md).
* Zapoznaj się z [usługi Azure Monitor](../azure-monitor/overview.md) Przegląd informacje dotyczące ustawień zaawansowanych dziennika, włączana za pomocą usługi Azure Monitor.
* Zapoznaj się z artykułem [zbieranie i używanie dane dzienników z zasobów platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md) dotyczących konfigurowania ustawienia dziennika diagnostycznego Twins cyfrowych platformy Azure za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

Po skonfigurowaniu będzie mogła wybierz wszystkie kategorie dzienników, metryk i użycie obszary robocze usługi zaawansowane monitorowanie usługi Azure log analytics do wspierania wysiłków debugowania.

### <a name="trace-sensor-telemetry"></a>Telemetria śledzenia usługi czujnika

Śledź dane telemetryczne z czujników, sprawdź, czy ustawienia diagnostyczne są włączone dla swojego wystąpienia Twins cyfrowych platformy Azure. Następnie upewnij się, czy wszystkie żądane kategorie dziennika są zaznaczone. Ponadto upewnij się, że wybrane dzienniki są wysyłane do usługi Azure Monitor dzienniki.

Aby dopasować komunikaty telemetryczne czujnik, jego odpowiednich dzienniki, można określić identyfikator korelacji na dane zdarzenia są wysyłane. Aby to zrobić, należy ustawić `x-ms-client-request-id` właściwość identyfikatora GUID.

Po wysłaniu danych telemetrycznych, Otwórz program log analytics, aby zapytanie o dzienniki przy użyciu zestawu identyfikator korelacji:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Wartość zapytania | Zamień na |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Identyfikator korelacji, który został określony na dane zdarzeń |

Włączenie rejestrowania dla funkcji zdefiniowanych przez użytkownika tych dzienników są wyświetlane w wystąpieniu log analytics z kategorią `UserDefinedFunction`. Aby je pobrać, wprowadź następujące warunki zapytania w usłudze log analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Aby uzyskać więcej informacji na temat kwerend operacji odczytu [wprowadzenie do zapytań](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Zidentyfikować typowe problemy

Diagnozowanie i identyfikuje typowe problemy są ważne podczas rozwiązywania problemów z rozwiązania. Kilka problemów najczęściej występujących podczas tworzenia funkcji zdefiniowanych przez użytkownika są podsumowane w poniższe podsekcje.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Sprawdź, czy przydział roli został utworzony

Bez przypisania roli, utworzone w ramach interfejsu API zarządzania funkcja zdefiniowana przez użytkownika nie ma dostępu do wykonywać żadnych akcji, takie jak wysyłanie powiadomień, pobierania metadanych, i ustawienie obliczonych wartości w ramach topologii.

Sprawdź, czy przydział roli istnieje funkcji zdefiniowanych przez użytkownika za pomocą usługi API Management:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Wartość parametru | Zamień na |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika do pobrania przypisań ról|

Dowiedz się, [jak można utworzyć przypisania roli, funkcji zdefiniowanych przez użytkownika](./how-to-user-defined-functions.md), jeśli istnieje nie przypisań ról.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Sprawdź, czy dopasowywania działa dla czujnika telemetrii

Przy użyciu następującego wywołania względem interfejsu API zarządzania wystąpieniami danego Twins cyfrowych platformy Azure możesz określić, czy dany dopasowywania ma zastosowanie do danego czujnika.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Identyfikator dopasowywania, którą chcesz ocenić |
| *YOUR_SENSOR_IDENTIFIER* | Identyfikator czujnik, który chcesz ocenić |

Odpowiedź:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Sprawdź, co wyzwala czujnika

Przy użyciu następującego wywołania względem cyfrowego Twins zarządzania interfejsów API usługi Azure możesz określić identyfikatory zdefiniowane przez użytkownika funkcji wyzwalanej przez dany czujnik przychodzących danych telemetrycznych:

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

### <a name="issue-with-receiving-notifications"></a>Problem z otrzymywania powiadomień

Jeśli nie otrzymujesz powiadomień wyzwalane funkcji zdefiniowanej przez użytkownika, upewnij się, że Twój parametr typu obiektu topologia jest zgodny z typem identyfikatora, który jest używany.

**Niepoprawne** przykładu:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

W tym scenariuszu pojawia się ponieważ używanych identyfikator odwołuje się do czujnika, gdy określony typ obiektu topologii, jest `Space`.

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

1. **Ograniczanie**: Jeśli funkcji zdefiniowanych przez użytkownika przekracza szybkość wykonywania limity opisane w [limity](./concepts-service-limits.md) artykułu, zostanie ograniczona. Do momentu wygaśnięcia limity ograniczania, żadne dalsze operacje są wykonywane pomyślnie.

1. **Nie można odnaleźć danych**: Jeśli funkcji zdefiniowanych przez użytkownika próbuje uzyskać dostęp metadanych, który nie istnieje, operacja zakończy się niepowodzeniem.

1. **Nieautoryzowane**: Jeśli nie ma zdefiniowanej przez użytkownika funkcji przypisania roli ustawiona lub nie ma wystarczające uprawnienia dostępu do niektórych metadanych w topologii, kończy się niepowodzeniem.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak włączyć [monitorowania i dzienniki](./how-to-configure-monitoring.md) w reprezentacji urządzeń cyfrowych platformy Azure.

- Odczyt [dziennik aktywności — omówienie platformy Azure](../azure-monitor/platform/activity-logs-overview.md) artykułu, aby uzyskać więcej opcji platformy Azure rejestrowania.
