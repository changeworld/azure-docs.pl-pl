---
title: Usługa Azure Security Center dla przewodnika badania urządzenia IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: To jak przewodnik wyjaśnia, jak badania podejrzane urządzeń IoT za pomocą usługi Log Analytics przy użyciu Centrum zabezpieczeń Azure dla IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 6182662cb0da7fa5bcd3f329ada9ca5851490724
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60007886"
---
# <a name="investigate-a-suspicious-iot-device"></a>Zbadanie podejrzanych urządzeń IoT

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Security Center (ASC) alertów usługi IoT i dowody udzielać wyraźne wskazania urządzenia IoT są podejrzane o udział w podejrzane działania lub gdy oznaczeń istnieje, że urządzenie zostanie naruszony. 

W tym przewodniku należy użyć sugestie badania, pod warunkiem, aby ustalić potencjalne zagrożenia dla Twojej organizacji, należy zdecydować, jak rozwiązania i najlepszych sposobów, aby zapobiec atakom podobne w przyszłości.  

> [!div class="checklist"]
> * Znajdowanie danych urządzenia
> * Badanie przy użyciu zapytań kql


## <a name="how-can-i-access-my-data"></a>Jak można uzyskać dostęp Moje dane?

Domyślnie ASC IoT przechowuje alerty zabezpieczeń i zalecenia w obszarze roboczym usługi Log Analytics. Można również przechowywać dane pierwotne zabezpieczeń.

Aby zlokalizować obszaru roboczego usługi Log Analytics do przechowywania danych:

1. Otwórz Centrum IoT hub, 
1. W obszarze **zabezpieczeń**, kliknij przycisk **Przegląd**, a następnie wybierz pozycję **ustawienia**.
1. Zmień szczegóły konfiguracji obszaru roboczego usługi Log Analytics. 
1. Kliknij pozycję **Zapisz**. 

Po konfiguracji wykonaj następujące czynności na dostęp do danych przechowywanych w obszarze roboczym usługi Log Analytics:

1. Wybierz, a następnie kliknij pozycję ASC IoT alertu w usłudze IoT Hub. 
1. Kliknij przycisk **dalsze badanie**. 
1. Wybierz **urządzeń, które mają ten alert, kliknij tutaj, aby wyświetlić kolumnę DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Kroki badania dotyczące podejrzanych urządzeń IoT

Aby uzyskać dostęp do szczegółowych informacji i nieprzetworzonych danych dotyczących urządzeń IoT, przejdź do obszaru roboczego usługi Log Analytics [dostęp do danych](#how-can-i-access-my-data).

Sprawdź i badanie danych urządzenia następujące szczegółowe informacje i działań przy użyciu następujących zapytań kql.

### <a name="related-alerts"></a>Powiązanych alertów

Aby dowiedzieć się, jeśli były inne alerty wyzwalane na tym samym czasie używana następująca kwerenda kql:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Użytkownicy z dostępem

Aby dowiedzieć się, którzy użytkownicy mają dostęp do tego urządzenia, należy użyć następującej kwerendy kql: 

  ~~~
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
  ~~~
Aby dowiedzieć się, należy użyć tych danych: 
  1. Którzy użytkownicy mają dostęp do urządzenia?
  2. Użytkownicy z dostępem mają poziomy uprawnień, zgodnie z oczekiwaniami? 

### <a name="open-ports"></a>Otwieranie portów

Aby dowiedzieć się, które porty na urządzeniu są obecnie używane lub zastosowano, użyj następującego zapytania kql: 

  ~~~
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
  ~~~

    Use this data to discover:
  1. Które nasłuchiwania gniazda są obecnie aktywne, w urządzeniu?
  2. Powinien być dozwolony nasłuchiwania gniazd, które są aktualnie aktywne
  3. Czy istnieją podejrzanych adresów zdalnych podłączone do urządzenia?

### <a name="user-logins"></a>Identyfikatory logowania użytkownika

Aby dowiedzieć się, użytkownicy, którzy zalogowany do urządzenia, użyj następującego zapytania kql: 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. Użytkowników, którzy zalogowany do urządzenia?
  2. To użytkownicy, którzy zalogowali się, powinien się zalogować?
  3. Czy użytkownicy, którzy zalogowali połączyć z oczekiwanych lub nieoczekiwany adresów IP?
  
### <a name="process-list"></a>Lista procesów

Aby dowiedzieć się, jeśli lista procesu jest zgodne z oczekiwaniami, użyj następującego zapytania kql: 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. Pojawili się tam podejrzanych procesów działających na urządzeniu?
  2. Zostały procesy wykonywane przez użytkowników odpowiednich?
  3. Liczba wykonań dowolnego wiersza polecenia zawierała prawidłowego i oczekiwane argumenty?

## <a name="next-steps"></a>Kolejne kroki

Po zbadaniu urządzenia i uzyskania lepszego zrozumienia ryzyka, warto wziąć pod uwagę [Konfigurowanie alertów niestandardowych](quickstart-create-custom-alerts.md) do zwiększenia poziomu bezpieczeństwa rozwiązań IoT. Jeśli nie masz jeszcze agent urządzenia, należy wziąć pod uwagę [wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md) lub [zmiana konfiguracji istniejącego agenta urządzenia](how-to-agent-configuration.md) aby poprawić wyniki. 