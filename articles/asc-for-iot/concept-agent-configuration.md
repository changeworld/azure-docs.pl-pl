---
title: Konfigurowanie usługi ASC agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować agentów do użycia z usługą ASC dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3a3806c73ef254abab91bd28cd8761917371235f
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541920"
---
# <a name="configure-security-agents"></a>Konfigurowanie zabezpieczeń agentów

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera wyjaśnienie, jak skonfigurować agenta do użycia z usługą ASC dla IoT.

## <a name="agents"></a>Agenci

ASC agentów zabezpieczeń IoT zbieranie danych z urządzeń IoT i wykonywać akcje zabezpieczeń, aby rozwiązać wykryte luki w zabezpieczeniach. Konfiguracja agenta zabezpieczeń jest kontrolowane, przy użyciu zestawu właściwości bliźniaczej reprezentacji modułu, które można dostosować. Ogólnie rzecz biorąc dodatkowej aktualizacji tych właściwości są rzadkie.  

ASC obiekt konfiguracji bliźniaczej reprezentacji agenta zabezpieczeń IoT firmy jest obiektem formatu JSON. Obiekt konfiguracji to zbiór kontrolowania właściwości, które można zdefiniować w celu sterowania zachowaniem agenta. 

Te konfiguracje pomóc dostosować agenta dla każdego scenariusza wymagana. Na przykład automatycznie wyłączając niektóre zdarzenia lub utrzymywanie zużycie energii do minimalnego poziomu są możliwe, konfigurując tych właściwości.  

Użyj usługi ASC konfiguracji agenta zabezpieczeń IoT [schematu](https://github.com/azure/asc-for-iot-schemas/security/module/twin) zmian.  

## <a name="configuration-objects"></a>Obiekty konfiguracji 

Właściwość znajduje się wewnątrz obiektu konfiguracji agenta w ramach sekcji żądane właściwości modułu azureiotsecurity związane z każdego ASC agent zabezpieczeń IoT. 

Aby zmodyfikować konfigurację, tworzenia i modyfikacji obiektu wewnątrz azureiotsecurity tożsamości bliźniaczej reprezentacji modułu. Jeśli obiekt konfiguracji agenta nie istnieje w bliźniaczej reprezentacji modułu azureiotsecurity, wszystkie wartości właściwości agenta zabezpieczeń są ustawiane jako domyślna. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //Agent configuration object 
  … 
} 
}
```

Upewnij się, że weryfikacja zmian konfiguracji agenta przed tym [schematu](https://aka.ms/iot-security-github-module-schema).
Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.


## <a name="editing-a-property"></a>Edytowanie właściwości 

Wszystkie właściwości niestandardowe, należy ustawić wewnątrz obiektu konfiguracji agenta w bliźniaczej reprezentacji modułu azureiotsecurity. 

Konfigurowanie właściwości przesłaniać wartość domyślną. Do ustawiania właściwości, należy dodać klucz właściwości do obiektu konfiguracji z żądaną wartość. 

Aby użyć domyślnej wartości właściwości, należy usunąć właściwość z obiektu konfiguracji. 

```json
"desired": { //azureiotsecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "High" 
  } 
}, 
```

## <a name="default-properties"></a>Właściwości domyślne 
Zestaw musi właściwości, które kontrolują ASC agentów zabezpieczeń IoT.

Wartości domyślne są dostępne w odpowiednich schemat w [Github](https://aka.ms/iot-security-module-default).

| Name (Nazwa)| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Wymagane: false |Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT7M |Maksymalny czas przed komunikatami o wysokim priorytecie są wysyłane.|
|lowPriorityMessageFrequency |Wymagane: false|Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT5H |Maksymalny czas przed komunikatami o niskim priorytecie są wysyłane.| 
|snapshotFrequency |Wymagane: false|Prawidłowe wartości: czas trwania w ISO 8601 Format |Wartość domyślna PT13H |Interwał tworzenia migawki stanu urządzenia.| 
|maxLocalCacheSizeInBytes |Wymagane: false |Prawidłowe wartości: |Wartość domyślna: 2560000 większy niż 8192 | Pamięci masowej (w bajtach) dozwoloną dla pamięci podręcznej komunikatu o agenta. Maksymalna ilość miejsca do przechowywania komunikatów na urządzeniu, przed wysłaniem wiadomości.| 
|maxMessageSizeInBytes |Wymagane: false |Prawidłowe wartości: Liczba dodatnia większa niż 8192 mniej niż 262144 |Wartość domyślna: 204800 |Maksymalny dozwolony rozmiar równy agenta pod kątem komunikatów w chmurze. To ustawienie steruje maksymalną dane przesyłane w każdej wiadomości. |
|eventPriority${EventName} |Wymagane: false |Prawidłowe wartości: Wysoki, niski wyłączone |Wartości domyślne: |Priorytet każdego agenta wygenerowanych zdarzeń | 

### <a name="events"></a>Zdarzenia

|Nazwa zdarzenia| PropertyName | Wartość domyślna| Zdarzenie migawki| Szczegóły stanu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Zdarzenie diagnostyki|eventPriorityDiagnostic| Wyłączone| False| Agent powiązanych zdarzeń diagnostycznych. Aby uzyskać pełne rejestrowanie przy użyciu tego zdarzenia.| 
|Błąd konfiguracji |eventPriorityConfigurationError |Małe |False |Nie można przeanalizować konfiguracji agenta. Sprawdź konfigurację względem schematu.| 
|Statystyki porzuconych zdarzeń |eventPriorityDroppedEventsStatistics |Małe |True|Agent powiązanych Statystyka zdarzeń. |
|Statystyki komunikatów|eventPriorityMessageStatistics |Małe |True |Agent powiązanych statystyki o komunikatach. |
|Podłączonych urządzeń|eventPriorityConnectedHardware |Małe |True |Migawka całego sprzętu połączonych z urządzeniem.|
|Porty nasłuchiwania|eventPriorityListeningPorts |Wysoka |True |Migawka wszystkie otwarte porty nasłuchiwania na urządzeniu.|
|Tworzenie procesu |eventPriorityProcessCreate |Małe |False |Inspekcje przetworzyć tworzenia na urządzeniu.|
|Zakończenie procesu|eventPriorityProcessTerminate |Małe |False |Inspekcje przetworzyć zakończenie na urządzeniu.| 
|Informacje o systemie |eventPrioritySystemInformation |Małe |True |Migawka informacje o systemie (na przykład: System operacyjny lub procesor CPU).| 
|Użytkownicy lokalni| eventPriorityLocalUsers |Wysoka |True|Migawkę zarejestrowanych użytkowników lokalnych skonfigurowanych w ramach systemu. |
|Login|  eventPriorityLogin |Wysoka|False|Przeprowadź inspekcję zdarzeń logowania na urządzeniu (logowania lokalnego i zdalnego).|
|Tworzenie połączenia |eventPriorityConnectionCreate|Małe|False|Przeprowadza inspekcję połączeń TCP utworzone do i z urządzenia. |
|Konfiguracja zapory| eventPriorityFirewallConfiguration|Małe|True|Migawki konfiguracji zapory urządzenia (reguły zapory). |
|Punktem odniesienia systemu operacyjnego| eventPriorityOSBaseline| Małe|True|Sprawdź migawkę odniesienia systemu operacyjnego urządzenia.|
 

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi ASC zaleceń IoT](concept-recommendations.md)
- [Zapoznaj się z usługą ASC alertów IoT](concept-security-alerts.md)
- [Dostęp do danych pierwotnych zabezpieczeń](how-to-security-data-access.md)