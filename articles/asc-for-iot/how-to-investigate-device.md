---
title: Badanie podejrzanego urządzenia
description: W tym artykule wyjaśniono, jak używać usługi Azure Security Center dla IoT w celu zbadania podejrzanego urządzenia IoT przy użyciu usługi Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: f333f28dc0e02e8d010f5521f298d0f0b031dbf2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311045"
---
# <a name="investigate-a-suspicious-iot-device"></a>Badanie podejrzanego urządzenia IoT

Alerty usługi IoT usługi Usługi Azure Security Center zapewniają wyraźne wskazania, gdy podejrzewa się, że urządzenia IoT są zaangażowane w podejrzane działania lub gdy istnieją wskazania, że urządzenie zostało naruszone.

W tym przewodniku skorzystaj z sugestii dotyczących badania, aby określić potencjalne zagrożenia dla organizacji, zdecydować, jak skorygować i odkryć najlepsze sposoby zapobiegania podobnym atakom w przyszłości.

> [!div class="checklist"]
> * Znajdowanie danych urządzenia
> * Badanie przy użyciu zapytań kql

## <a name="how-can-i-access-my-data"></a>Jak mogę uzyskać dostęp do moich danych?

Domyślnie usługa Azure Security Center dla IoT przechowuje alerty zabezpieczeń i zalecenia w obszarze roboczym usługi Log Analytics. Możesz również zapisać swoje surowe dane zabezpieczające.

Aby zlokalizować obszar roboczy usługi Log Analytics w celu przechowywania danych:

1. Otwórz centrum IoT,
1. W obszarze **Zabezpieczenia**kliknij pozycję **Przegląd**, a następnie wybierz pozycję **Ustawienia**.
1. Zmień szczegóły konfiguracji obszaru roboczego usługi Log Analytics.
1. Kliknij pozycję **Zapisz**.

Następująca konfiguracja, aby uzyskać dostęp do danych przechowywanych w obszarze roboczym usługi Log Analytics:

1. Wybierz i kliknij alert Usługi Azure Security Center dla IoT w centrum IoT Hub.
1. Kliknij **przycisk Dalsze dochodzenie**.
1. Wybierz, **aby zobaczyć, które urządzenia mają ten alert, kliknij tutaj i wyświetl kolumnę Identyfikator urządzenia**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Czynności dochodzeniowe dotyczące podejrzanych urządzeń IoT

Aby wyświetlić szczegółowe informacje i surowe dane dotyczące urządzeń IoT, przejdź do obszaru roboczego usługi Log Analytics, [aby uzyskać dostęp do danych.](#how-can-i-access-my-data)

Zapoznaj się z poniższymi przykładowymi zapytaniami kql, aby rozpocząć badanie alertów i działań na urządzeniu.

### <a name="related-alerts"></a>Powiązane alerty

Aby dowiedzieć się, czy inne alerty zostały wyzwolone w tym samym czasie, użyj następującej kwerendy kql:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Użytkownicy z dostępem

Aby dowiedzieć się, którzy użytkownicy mają dostęp do tego urządzenia, użyj następującej kwerendy kql:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Użyj tych danych, aby odkryć:

- Którzy użytkownicy mają dostęp do urządzenia?
- Czy użytkownicy z dostępem mają oczekiwane poziomy uprawnień?

### <a name="open-ports"></a>Otwieranie portów

Aby dowiedzieć się, które porty w urządzeniu są obecnie używane lub były używane, użyj następującej kwerendy kql:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Użyj tych danych, aby odkryć:

- Które gniazda nasłuchiwania są obecnie aktywne w urządzeniu?
- Czy gniazda nasłuchiwania, które są obecnie aktywne, powinny być dozwolone?
- Czy są jakieś podejrzane adresy zdalne podłączone do urządzenia?

### <a name="user-logins"></a>Loginy użytkowników

Aby znaleźć użytkowników zalogowanych do urządzenia, użyj następującej kwerendy kql:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Użyj wyników kwerendy, aby odkryć:

- Którzy użytkownicy zalogowali się do urządzenia?
- Czy użytkownicy, którzy się zalogowali, mają się zalogować?
- Czy użytkownicy zalogowani połączyli się z oczekiwanych lub nieoczekiwanych adresów IP?

### <a name="process-list"></a>Lista procesów

Aby dowiedzieć się, czy lista procesów jest zgodnie z oczekiwaniami, użyj następującej kwerendy kql:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Użyj wyników kwerendy, aby odkryć:

- Czy na urządzeniu działały jakieś podejrzane procesy?
- Czy procesy były wykonywane przez odpowiednich użytkowników?
- Czy wykonanie wiersza polecenia zawierało poprawne i oczekiwane argumenty?

## <a name="next-steps"></a>Następne kroki

Po zbadaniu urządzenia i uzyskaniu lepszego zrozumienia zagrożeń warto rozważyć [konfigurowanie alertów niestandardowych](quickstart-create-custom-alerts.md) w celu poprawy stanu zabezpieczeń rozwiązania IoT. Jeśli nie masz jeszcze agenta urządzenia, należy rozważyć [wdrożenie agenta zabezpieczeń](how-to-deploy-agent.md) lub [zmianę konfiguracji istniejącego agenta urządzenia,](how-to-agent-configuration.md) aby poprawić wyniki.
