---
title: Jak debugować pliki UDF - Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zalecanych podejściach do debugowania funkcji zdefiniowanych przez użytkownika w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511641"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Jak debugować funkcje zdefiniowane przez użytkownika w usłudze Azure Digital Twins

W tym artykule podsumowano sposób diagnozowania i debugowania funkcji zdefiniowanych przez użytkownika w usłudze Azure Digital Twins. Następnie identyfikuje niektóre z najbardziej typowych scenariuszy znalezionych podczas debugowania ich.

>[!TIP]
> Przeczytaj [jak skonfigurować monitorowanie i rejestrowanie,](./how-to-configure-monitoring.md) aby dowiedzieć się więcej o konfigurowaniu narzędzi debugowania w programie Azure Digital Twins przy użyciu dzienników aktywności, dzienników diagnostycznych i usługi Azure Monitor.

## <a name="debug-issues"></a>Problemy z debugowaniem

Wiedza o tym, jak diagnozować problemy w programie Azure Digital Twins, umożliwia efektywne analizowanie problemów, identyfikowanie przyczyn problemów i zapewnianie odpowiednich rozwiązań dla nich.

W tym celu dostępnych jest wiele narzędzi do rejestrowania, analizy i diagnostyki.

### <a name="enable-logging-for-your-instance"></a>Włączanie rejestrowania dla wystąpienia

Usługa Azure Digital Twins obsługuje niezawodne rejestrowanie, monitorowanie i analizy. Deweloperzy rozwiązań mogą używać dzienników usługi Azure Monitor, dzienników diagnostycznych, dzienników aktywności i innych usług do obsługi złożonych potrzeb monitorowania aplikacji IoT. Opcje rejestrowania można łączyć w celu wykonywania zapytań lub wyświetlania rekordów w kilku usługach oraz w celu zapewnienia szczegółowego rejestrowania dla wielu usług.

* Aby uzyskać konfigurację rejestrowania specyficzną dla usługi Azure Digital Twins, przeczytaj [artykuł Jak skonfigurować monitorowanie i rejestrowanie](./how-to-configure-monitoring.md).
* Zapoznaj się z omówieniem [usługi Azure Monitor,](../azure-monitor/overview.md) aby dowiedzieć się więcej o zaawansowanych ustawieniach dziennika włączonych za pośrednictwem usługi Azure Monitor.
* Zapoznaj się z artykułem [Zbieranie i korzystanie z danych dziennika z zasobów platformy Azure](../azure-monitor/platform/platform-logs-overview.md) do konfigurowania ustawień dziennika diagnostycznego w usłudze Azure Digital Twins za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

Po skonfigurowaniu będzie można wybrać wszystkie kategorie dziennika, metryki i używać zaawansowanych obszarów roboczych analizy dzienników usługi Azure Monitor do obsługi wysiłków debugowania.

### <a name="trace-sensor-telemetry"></a>Telemetria czujnika śledzenia

Aby śledzić dane telemetryczne czujnika, sprawdź, czy ustawienia diagnostyczne są włączone dla wystąpienia usługi Azure Digital Twins. Następnie upewnij się, że wszystkie żądane kategorie dziennika są zaznaczone. Na koniec upewnij się, że żądane dzienniki są wysyłane do dzienników usługi Azure Monitor.

Aby dopasować komunikat telemetrii czujnika do odpowiednich dzienników, można określić identyfikator korelacji na dane zdarzenia wysyłane. Aby to zrobić, `x-ms-client-request-id` ustaw właściwość na identyfikator GUID.

Po wysłaniu danych telemetrycznych otwórz analizę dziennika usługi Azure Monitor, aby zbadać dzienniki przy użyciu ustawionego identyfikatora korelacji:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Wartość kwerendy | Zamień na |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Identyfikator korelacji określony w danych zdarzenia |

Aby odczytać wszystkie ostatnie kwerendy dzienników telemetrycznych:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Jeśli włączysz rejestrowanie funkcji zdefiniowanej przez użytkownika, dzienniki te pojawią się w wystąpieniu analizy dziennika z kategorią `UserDefinedFunction`. Aby je pobrać, wprowadź następujący warunek kwerendy w analizie dziennika:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Aby uzyskać więcej informacji na temat zaawansowanych operacji kwerend, zobacz [Wprowadzenie do kwerend](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Identyfikowanie typowych problemów

Zarówno diagnozowanie, jak i identyfikowanie typowych problemów są ważne podczas rozwiązywania problemów z rozwiązaniem. Kilka problemów, które są często spotykane podczas tworzenia funkcji zdefiniowanych przez użytkownika są podsumowane w następujących podsekcjach.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Sprawdzanie, czy utworzono przypisanie roli

Bez przypisania roli utworzonego w interfejsie API zarządzania funkcja zdefiniowana przez użytkownika nie ma dostępu do wykonywania żadnych akcji, takich jak wysyłanie powiadomień, pobieranie metadanych i ustawianie obliczonych wartości w topologii.

Sprawdź, czy istnieje przypisanie roli dla funkcji zdefiniowanej przez użytkownika za pośrednictwem interfejsu API zarządzania:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Wartość parametru | Zamień na |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Identyfikator funkcji zdefiniowanej przez użytkownika w celu pobierania przypisań ról dla|

Dowiedz [się, jak utworzyć przypisanie roli dla funkcji zdefiniowanej przez użytkownika,](./how-to-user-defined-functions.md)jeśli nie istnieją żadne przypisania ról.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Sprawdź, czy moduł zapałka działa dla telemetrii czujnika

Za pomocą następującego wywołania względem interfejsu API zarządzania wystąpieniami usług Azure Digital Twins możesz określić, czy dany matcher ma zastosowanie do danego czujnika.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Identyfikator matchera, który chcesz ocenić |
| *YOUR_SENSOR_IDENTIFIER* | Identyfikator czujnika, który chcesz ocenić |

Odpowiedź:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Sprawdzanie, co wyzwala czujnik

With the following call against the Azure Digital Twins Management APIs, you're able to determine the identifiers of your user-defined functions triggered by the given sensor's incoming telemetry:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parametr | Zamień na |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Identyfikator czujnika do wysyłania danych telemetrycznych |

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

Jeśli nie otrzymujesz powiadomień z wyzwalanej funkcji zdefiniowanej przez użytkownika, upewnij się, że parametr typu obiektu topologii jest zgodny z typem identyfikatora, który jest używany.

**Niepoprawne** Przykład:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Ten scenariusz powstaje, ponieważ używany identyfikator odnosi się do czujnika, `Space`podczas gdy określony typ obiektu topologii jest .

**Prawidłowe** Przykład:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Najprostszym sposobem, aby nie uruchomić w `Notify` tym problemie jest użycie metody na obiekt metadanych.

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

## <a name="common-diagnostic-exceptions"></a>Typowe wyjątki diagnostyczne

Jeśli włączysz ustawienia diagnostyczne, mogą wystąpić następujące typowe wyjątki:

1. **Ograniczanie**przepustowości: jeśli funkcja zdefiniowana przez użytkownika przekracza limity szybkości wykonywania opisane w artykule [Limity usług,](./concepts-service-limits.md) zostanie ona ograniczona. Żadne dalsze operacje nie są wykonywane pomyślnie, dopóki limity ograniczania przepustowości nie wygasną.

1. **Nie znaleziono danych:** jeśli funkcja zdefiniowana przez użytkownika próbuje uzyskać dostęp do metadanych, które nie istnieją, operacja zakończy się niepowodzeniem.

1. **Nie autoryzowany:** jeśli funkcja zdefiniowana przez użytkownika nie ma zestawu przypisania roli lub nie ma wystarczających uprawnień dostępu do niektórych metadanych z topologii, operacja zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak włączyć [monitorowanie i logowanie](./how-to-configure-monitoring.md) w usłudze Azure Digital Twins.

- Przeczytaj [omówienie](../azure-monitor/platform/platform-logs-overview.md) dziennika aktywności platformy Azure artykuł więcej opcji rejestrowania platformy Azure.
