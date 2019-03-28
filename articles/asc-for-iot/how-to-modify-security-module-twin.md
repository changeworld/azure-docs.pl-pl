---
title: Jak instrukcje, aby zmodyfikować ASC bliźniaczą reprezentację modułu IoT w ASC dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmodyfikować ASC IoT zabezpieczeń bliźniaczą reprezentację modułu ASC dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541950"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Modyfikowanie ASC bliźniaczą reprezentację modułu IoT

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak zmodyfikować konfigurację istniejącego **bliźniaczą reprezentację modułu AzureIoTSecurity** dla istniejącego urządzenia. 

Zobacz [tworzenie ASC moduł IoT](quickstart-create-security-twin.md) Aby dowiedzieć się, jak utworzyć nowy moduł zabezpieczeń dla nowego urządzenia.  

## <a name="modification-considerations"></a>Zagadnienia dotyczące modyfikacji

> [!IMPORTANT]
> Każda konfiguracja w konfiguracji bliźniaczej reprezentacji przesłania konfigurację domyślną. Jeśli konkretnej konfiguracji nie jest już obecny w konfiguracji bliźniaczej reprezentacji, jest używana domyślna konfiguracja. 

## <a name="configuration-schema-and-validation"></a>Schemat konfiguracji i sprawdzanie poprawności 

Upewnij się, że sprawdzanie poprawności konfiguracji agenta przed tym [schematu](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.

 
Jeśli agent jest uruchomiona, obiekt konfiguracji została zmieniona na nie jest ważna configuration (Konfiguracja nie jest zgodny ze schematem), agent będzie ignorować nieprawidłowej konfiguracji i będą nadal używać bieżącej konfiguracji. 

## <a name="edit-a-property"></a>Edytowanie właściwości  

Ustaw wszystkie niestandardowe właściwości wewnątrz obiektu konfiguracji agenta w bliźniaczej reprezentacji modułu AzureIoTSecurity. 

Do ustawiania właściwości, należy dodać klucz właściwości do obiektu konfiguracji z żądaną wartość. Aby użyć domyślnej wartości właściwości, należy usunąć właściwość z obiektu konfiguracji:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Właściwości 
Poniższa tabela zawiera wszystkie konfigurowalne właściwości, które kontrolują ASC agentów IoT. 
          

| Name (Nazwa)| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Wymagane: false |Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT7M |Maksymalny czas przed komunikatami o wysokim priorytecie są wysyłane.|
|lowPriorityMessageFrequency |Wymagane: false|Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT5H |Maksymalny czas przed komunikatami o niskim priorytecie są wysyłane.| 
|snapshotFrequency |Wymagane: false|Prawidłowe wartości: czas trwania w ISO 8601 Format |Wartość domyślna PT13H |Interwał tworzenia migawki stanu urządzenia.| 
|maxLocalCacheSizeInBytes |Wymagane: false |Prawidłowe wartości: |Wartość domyślna: 2560000 większy niż 8192 | Pamięci masowej (w bajtach) dozwoloną dla pamięci podręcznej komunikatu o agenta. Maksymalna ilość miejsca do przechowywania komunikatów na urządzeniu, przed wysłaniem wiadomości.| 
|maxMessageSizeInBytes |Wymagane: false |Prawidłowe wartości: Liczba dodatnia większa niż 8192 mniej niż 262144 |Wartość domyślna: 204800 |Maksymalny dozwolony rozmiar równy agenta pod kątem komunikatów w chmurze. To ustawienie steruje maksymalną dane przesyłane w każdej wiadomości. |
|eventPriority${EventName} |Wymagane: false |Prawidłowe wartości: Wysoki, niski wyłączone |Wartości domyślne: |Priorytet każdego agenta jest generowane zdarzenie. | 
|

### <a name="events"></a>Zdarzenia

Poniższa lista zdarzeń są wszystkie zdarzenia ASC IoT agent może zbierać z urządzeń. Bliźniacza reprezentacja modułu AzureIotSecurity umożliwia skonfigurowanie, którym te zdarzenia są zbierane i określić ich priorytet w rozwiązaniu. 
 
|Nazwa zdarzenia| PropertyName | Wartość domyślna| Zdarzenie migawki| Szczegóły stanu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Zdarzenie diagnostyki|eventPriorityDiagnostic| Wyłączone| False| Agent powiązanych zdarzeń diagnostycznych. Aby uzyskać pełne rejestrowanie przy użyciu tego zdarzenia.| 
Błąd konfiguracji |eventPriorityConfigurationError |Małe |False |Nie można przeanalizować konfiguracji agenta. Sprawdź konfigurację względem schematu.| 
|Statystyki porzuconych zdarzeń |eventPriorityDroppedEventsStatistics |Małe |True|Agent powiązanych Statystyka zdarzeń. |
|Statystyki komunikatów|eventPriorityMessageStatistics |Małe |True |Agent powiązanych statystyki o komunikatach. |
|Podłączonych urządzeń|eventPriorityConnectedHardware |Małe |True |Migawka całego sprzętu połączonych z urządzeniem.|
|Porty nasłuchiwania|eventPriorityListeningPorts |Wysoka |True |Migawka wszystkie otwarte porty nasłuchiwania na urządzeniu.|
| Tworzenie procesu |eventPriorityProcessCreate |Małe |False |Inspekcje przetworzyć tworzenia na urządzeniu.|
| Zakończenie procesu|eventPriorityProcessTerminate |Małe |False |Inspekcje przetworzyć zakończenie na urządzeniu.| 
 Informacje o systemie |eventPrioritySystemInformation |Małe |True |Migawkę informacje o systemie, takie jak system operacyjny lub procesora CPU.|
|Użytkownicy lokalni| eventPriorityLocalUsers |Wysoka |True|Migawkę zarejestrowanych użytkowników lokalnych skonfigurowanych w ramach systemu. |
|Login|  eventPriorityLogin |Wysoka|False|Przeprowadza inspekcję zdarzeń logowania na urządzeniu (logowania lokalnego i zdalnego).|
|Tworzenie połączenia |eventPriorityConnectionCreate|Małe|False|Przeprowadza inspekcję połączeń TCP utworzone do i z urządzenia. |
|Konfiguracja zapory| eventPriorityFirewallConfiguration|Małe|True|Migawki konfiguracji zapory urządzenia (reguły zapory). |
|Punktem odniesienia systemu operacyjnego| eventPriorityOSBaseline| Małe|True|Sprawdź migawkę odniesienia systemu operacyjnego urządzenia.| 
|


## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj ASC, aby uzyskać IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Poznawanie i eksplorowanie [ASC alertów IoT](concept-security-alerts.md)
- Dowiedz się, jak uzyskać dostęp do Twojego [danych zabezpieczeń](how-to-security-data-access.md)
