---
title: Omówienie zadań usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — Planowanie zadań do uruchamiania na wielu urządzeniach podłączonych do Centrum IoT. Zadania można zaktualizować tagi i żądane właściwości i wywoływanie metod bezpośrednich na wielu urządzeniach.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2018
ms.openlocfilehash: aacb0ab69dad45f9ca7655daaae0c2acff0403f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044376"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planowanie zadań na wielu urządzeniach

Usługa Azure IoT Hub umożliwia stosowanie szeregu bloki konstrukcyjne, takich jak [właściwości bliźniaczych reprezentacji urządzeń i tagi](iot-hub-devguide-device-twins.md) i [metody bezpośrednie](iot-hub-devguide-direct-methods.md). Zazwyczaj aplikacji zaplecza Włącz Administratorzy urządzenia i operatory zaktualizować i korzystać z urządzeń IoT w trybie zbiorczym, jak i w zaplanowanym czasie. Zadania wykonywania aktualizacji bliźniaczej reprezentacji urządzenia i metod bezpośrednich dla urządzeń w zaplanowanym czasie. Na przykład operator może użyć aplikacji zaplecza, która inicjuje i śledzi zadania ponownego uruchomienia urządzeń w tworzeniu 43 i piętro 3 w czasie, który nie jest znaczący wpływ na operacje tworzenia.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Rozważ użycie zadania gdy należy zaplanować i śledzić postęp dowolne z następujących działań w zestawie urządzeń:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

## <a name="job-lifecycle"></a>Cykl życia zadania

Zadania są inicjowane przez zaplecze rozwiązania i obsługiwane przez usługę IoT Hub. Możesz zainicjować zadania za pomocą identyfikatora URI przeznaczonych dla usługi (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) i wykonywania zapytań o postęp wykonywania zadania za pomocą identyfikatora URI przeznaczonych dla usługi (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`). Aby odświeżyć stan uruchomionych zadań po zainicjowaniu zadania, uruchom zapytanie zadania.

> [!NOTE]
> Po zainicjowaniu zadania nazwy i wartości właściwości mogą zawierać tylko US-ASCII drukowania litery, cyfry, z wyjątkiem tych w następującym zestawie: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Zadania do wykonania metody bezpośrednie

Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 wykonania [metoda bezpośrednia](iot-hub-devguide-direct-methods.md) na zbiór urządzeń, przy użyciu zadania:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleDirectMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Warunek kwerendy mogą być także identyfikator pojedynczego urządzenia lub na liście urządzeń identyfikatorów, jak pokazano w poniższych przykładach:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md) obejmuje język zapytań usługi IoT Hub w dodatkowych szczegółów.

## <a name="jobs-to-update-device-twin-properties"></a>Zadania można zaktualizować właściwości bliźniaczych reprezentacji urządzeń

Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 aktualizacji z właściwości bliźniaczych reprezentacji urządzeń przy użyciu zadania:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>

{
    "jobId": "<jobId>",
    "type": "scheduleTwinUpdate",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

## <a name="querying-for-progress-on-jobs"></a>Wykonywanie zapytań dotyczących postępu zadania

Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 zapytań dla zadań:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
Request-Id: <guid>
User-Agent: <sdk-name>/<sdk-version>
```

Token kontynuacji jest dostarczany z odpowiedzi.

Można tworzyć zapytania dotyczące stanu wykonywania zadania na temat korzystania z każdego urządzenia [język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Właściwości zadania

Na poniższej liście przedstawiono właściwości i opisy odpowiedniego, które mogą być używane podczas wykonywania zapytań dotyczących zadań lub wyniki zadania.

| Właściwość | Opis |
| --- | --- |
| **jobId** |Aplikacja podany identyfikator dla zadania. |
| **startTime** |Aplikacja podany czas rozpoczęcia (ISO-8601) dla zadania. |
| **endTime** |Usługa IoT Hub podana data (ISO-8601) ukończenia zadania. Prawidłowe tylko wtedy, gdy zadanie osiągnie stan "ukończone". |
| **type** |Rodzaje zadań: |
| | **scheduledUpdateTwin**: Zadanie, używane do aktualizowania zestawu żądane właściwości lub tagów. |
| | **scheduledDeviceMethod**: Zadania umożliwiają wywoływanie metody urządzenia na zestawie bliźniaczych reprezentacji urządzeń. |
| **status** |Bieżący stan zadania. Możliwe wartości dla stanu: |
| | **Oczekujące**: Zaplanowane i Trwa oczekiwanie na być pobierane przez usługę zadania. |
| | **Zaplanowane**: Zaplanowane na godzinę w przyszłości. |
| | **Uruchamianie**: Obecnie aktywnych zadań. |
| | **Anulowano**: Zadanie zostało anulowane. |
| | **Nie powiodło się**: Zadanie nie powiodło się. |
| | **Ukończono**: Zadanie zostało ukończone. |
| **deviceJobStatistics** |Statystyki dotyczące wykonywania zadania. |
| | **deviceJobStatistics** właściwości: |
| | **deviceJobStatistics.deviceCount**: Liczba urządzeń w ramach zadania. |
| | **deviceJobStatistics.failedCount**: Liczba urządzeń, w którym zadanie nie powiodło się. |
| | **deviceJobStatistics.succeededCount**: Liczba urządzeń, w którym zadanie zakończyło się pomyślnie. |
| | **deviceJobStatistics.runningCount**: Liczba urządzeń, które są aktualnie uruchomione zadania. |
| | **deviceJobStatistics.pendingCount**: Liczba urządzeń, oczekujące, aby uruchomić zadanie. |

### <a name="additional-reference-material"></a>Dodatkowe materiały

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) w tym artykule opisano różne punkty końcowe, które każde Centrum IoT hub udostępnia dla operacji zarządzania i środowiska wykonawczego.

* [Przydziału i ograniczanie przepływności](iot-hub-devguide-quotas-throttling.md) w tym artykule opisano przydziały, które mają zastosowanie do usługi IoT Hub i zachowanie ograniczania przepływności można oczekiwać, gdy korzystasz z usługi.

* [Usługa Azure IoT usługi zestawy SDK urządzeń i](iot-hub-devguide-sdks.md) Wyświetla język różnych zestawów SDK, można użyć podczas tworzenia aplikacji usług i urządzeń, które współdziałają z usługą IoT Hub.

* [Język zapytań usługi IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) opisuje język zapytań usługi IoT Hub. Użyj ten język zapytań, aby pobrać informacje z usługi IoT Hub o bliźniaczych reprezentacji urządzeń i zadań.

* [Obsługa protokołu MQTT Centrum IoT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi usługi IoT Hub dla protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki

Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczki usługi IoT Hub:

* [Planowanie i emitowanie zadań](iot-hub-node-node-schedule-jobs.md)