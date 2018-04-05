---
title: Zrozumienie zadania Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — Planowanie zadań do uruchamiania na wielu urządzeniach połączona z Centrum IoT. Zadania można zaktualizować znaczniki i odpowiednie właściwości i wywołania metod bezpośrednio na wielu urządzeniach.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 367eb703465b2fbc6f1e06a383bc9df709cabe78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planowanie zadań na wielu urządzeniach

Centrum IoT Azure umożliwia liczba bloków konstrukcyjnych, jak [dwie właściwości i urządzenia tagi] [ lnk-twin-devguide] i [bezpośrednie metody][lnk-dev-methods].  Zazwyczaj zaplecza aplikacji Włącz Administratorzy urządzenia i operatory do aktualizacji i interakcji z urządzeń IoT zbiorcze i w zaplanowanym terminie.  Zadania wykonywania aktualizacji dwie urządzenia i metody bezpośrednio pod kątem zestawu urządzeń w zaplanowanym terminie.  Na przykład operator użyje aplikacji zaplecza, która inicjuje i śledzi zadanie ponownego uruchomienia urządzeń z tworzeniem 43 i piętro 3 w czasie, który nie jest znaczący wpływ na operacje budynku.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Rozważ użycie zadań gdy należy zaplanować i śledzić postęp żadnego z następujących czynności na zbiór urządzeń:

* Aktualizowanie żądanych właściwości
* Tagi aktualizacji
* Wywołanie metody bezpośredniego

## <a name="job-lifecycle"></a>Cykl życia zadania
Zadania są inicjowane przez zaplecze rozwiązania i obsługiwanego przez Centrum IoT.  Możesz zainicjować zadania za pomocą identyfikatora URI usługi połączonej (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) i zapytanie o postęp wykonywania zadania za pomocą identyfikatora URI usługi połączonej (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Aby odświeżyć stan uruchomionych zadań po zainicjowaniu zadania, uruchomić zapytanie zadania.

> [!NOTE]
> Po zainicjowaniu zadania, nazwy i wartości właściwości mogą zawierać tylko US-ASCII drukowalnych alfanumeryczne, z wyjątkiem tych z następującego zestawu: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Zadania do wykonania metody bezpośredniego
Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 wykonywania [metoda bezpośrednia] [ lnk-dev-methods] na zbiór urządzeń przy użyciu zadania:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

Warunek kwerendy można też na identyfikator jednego urządzenia lub listę identyfikatorów, jak pokazano w poniższych przykładach urządzeń:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[Język zapytań Centrum IoT] [ lnk-query] obejmuje język zapytań Centrum IoT w dodatkowych szczegółów.

## <a name="jobs-to-update-device-twin-properties"></a>Zadania, aby zaktualizować urządzenie dwie właściwości
Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 aktualizowanie właściwości dwie urządzenia przy użyciu zadania:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>Wykonanie zapytania dotyczącego postępu zadania
Poniższy fragment kodu przedstawia szczegóły żądania HTTPS 1.1 [wykonywania zapytania dotyczącego zadania][lnk-query]:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

ContinuationToken jest dostarczany z odpowiedzi.  

## <a name="jobs-properties"></a>Właściwości zadania
Na poniższej liście przedstawiono właściwości i odpowiednie opisy, które mogą być używane podczas wykonywania zapytań dotyczących zadań lub wyniki zadania.

| Właściwość | Opis |
| --- | --- |
| **jobId** |Aplikacja podany identyfikator zadania. |
| **startTime** |Aplikacja podany czas rozpoczęcia (ISO 8601) dla zadania. |
| **endTime** |Centrum IoT podać datę (ISO 8601) ukończenia zadania. Jest prawidłowy tylko wtedy, gdy zadanie osiągnie stan "ukończone". |
| **Typ** |Typy zadań: |
| | **scheduledUpdateTwin**: zadanie używane do aktualizowania zestaw żądaną właściwości bądź tagi. |
| | **scheduledDeviceMethod**: zadanie służy do wywoływania metody urządzenia na zestawie twins urządzenia. |
| **status** |Bieżący stan zadania. Dopuszczalne wartości stanu: |
| | **Oczekujące**: Zaplanowane, oczekuje do pobrania przez usługę zadania. |
| | **Zaplanowane**: Zaplanowane na czas w przyszłości. |
| | **uruchomiona**: aktualnie aktywnego zadania. |
| | **Anulowane**: zadanie zostało anulowane. |
| | **nie powiodło się**: zadanie nie powiodło się. |
| | **Ukończono**: zadania zakończone. |
| **deviceJobStatistics** |Statystyka wykonywania zadania. |
| | **deviceJobStatistics** właściwości: |
| | **deviceJobStatistics.deviceCount**: liczba urządzeń w zadaniu. |
| | **deviceJobStatistics.failedCount**: liczba urządzeń, których zadania nie powiodło się. |
| | **deviceJobStatistics.succeededCount**: liczba urządzeń, w którym zadanie zakończyło się pomyślnie. |
| | **deviceJobStatistics.runningCount**: liczba urządzeń, które są aktualnie uruchomione zadanie. |
| | **deviceJobStatistics.pendingCount**: liczba urządzeń, które oczekują, aby uruchomić to zadanie. |

### <a name="additional-reference-material"></a>Odwołanie dodatkowe materiały
Inne tematy referencyjne w Podręczniku dewelopera Centrum IoT obejmują:

* [Punkty końcowe Centrum IoT] [ lnk-endpoints] opisano różne punkty końcowe, które udostępnia każdego centrum IoT dla operacji zarządzania i środowiska wykonawczego.
* [Ograniczenia przepustowości i przydziały] [ lnk-quotas] opisuje przydziałów, które dotyczą usługi IoT Hub i ograniczania przepustowości zachowania można oczekiwać podczas korzystania z usługi.
* [Zestawy Azure IoT urządzenia i usługi SDK] [ lnk-sdks] wymieniono języka różnych zestawów SDK, można użyć podczas opracowywania aplikacji usług i urządzeń, które współdziałają z Centrum IoT.
* [Język zapytań Centrum IoT urządzenia twins, zadań i rozsyłania wiadomości] [ lnk-query] opisuje język zapytań Centrum IoT. Użyj tego języka zapytań pobrać z Centrum IoT informacji o twins urządzenia i zadania.
* [Obsługa MQTT Centrum IoT] [ lnk-devguide-mqtt] zapewnia więcej informacji na temat Centrum IoT obsługi protokołu MQTT.

## <a name="next-steps"></a>Kolejne kroki
Aby wypróbować pojęcia opisane w tym artykule, zobacz następujące samouczek Centrum IoT:

* [Zadania harmonogramu i emisji][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
