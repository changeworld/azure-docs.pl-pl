---
title: Konfigurowanie usługi Azure Security Center dla agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować agentów do użycia z usługą Azure Security Center dla IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 1e212e752309fd8347836d462a3394da2fca4a15
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862781"
---
# <a name="tutorial-configure-security-agents"></a>Samouczek: Konfigurowanie alertów zabezpieczeń

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano usługi Azure Security Center (ASC) dla agenta zabezpieczeń IoT, w jaki sposób zmienić ASC je skonfigurować agentów zabezpieczeń IoT.

> [!div class="checklist"]
> * Konfigurowanie alertów zabezpieczeń
> * Zmień zachowanie agenta, edytując właściwości bliźniaczych reprezentacji
> * Odkryj konfiguracji domyślnej

## <a name="agents"></a>Agenci

ASC agentów zabezpieczeń IoT zbieranie danych z urządzeń IoT i wykonywać akcje zabezpieczeń, aby rozwiązać wykryte luki w zabezpieczeniach. Konfiguracja agenta zabezpieczeń jest kontrolowane, przy użyciu zestawu właściwości bliźniaczej reprezentacji modułu, które można dostosować. Ogólnie rzecz biorąc dodatkowej aktualizacji tych właściwości są rzadkie.  

ASC obiekt konfiguracji bliźniaczej reprezentacji agenta zabezpieczeń IoT firmy jest obiektem formatu JSON. Obiekt konfiguracji to zbiór kontrolowania właściwości, które można zdefiniować w celu sterowania zachowaniem agenta. 

Te konfiguracje pomóc dostosować agenta dla każdego scenariusza wymagana. Na przykład automatycznie wyłączając niektóre zdarzenia lub utrzymywanie zużycie energii do minimalnego poziomu są możliwe, konfigurując tych właściwości.  

Użyj usługi ASC konfiguracji agenta zabezpieczeń IoT [schematu](https://aka.ms/iot-security-github-module-schema) zmian.  

## <a name="configuration-objects"></a>Obiekty konfiguracji 

Każdy ASC agent zabezpieczeń IoT powiązane właściwości znajduje się w obiekt konfiguracji agenta w ramach sekcji żądanych właściwości z **azureiotsecurity** modułu. 

Aby zmodyfikować konfigurację, tworzenia i modyfikacji obiektu wewnątrz **azureiotsecurity** tożsamości bliźniaczej reprezentacji modułu. 

Jeśli nie istnieje obiekt konfiguracji agenta w **azureiotsecurity** bliźniaczą reprezentację modułu, wszystkie wartości właściwości agenta zabezpieczeń są ustawioną wartość domyślną. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Upewnij się, że weryfikacja zmian konfiguracji agenta przed tym [schematu](https://aka.ms/iot-security-github-module-schema).
Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.

## <a name="configuration-schema-and-validation"></a>Schemat konfiguracji i sprawdzanie poprawności 

Upewnij się, że sprawdzanie poprawności konfiguracji agenta przed tym [schematu](https://aka.ms/iot-security-github-module-schema). Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.

 
Jeśli agent jest uruchomiona, obiekt konfiguracji została zmieniona na nie jest ważna configuration (Konfiguracja nie jest zgodny ze schematem), agent będzie ignorować nieprawidłowej konfiguracji i będą nadal używać bieżącej konfiguracji. 

## <a name="editing-a-property"></a>Edytowanie właściwości 

Wszystkie właściwości niestandardowe, należy ustawić wewnątrz obiektu konfiguracji agenta w ramach **azureiotsecurity** bliźniaczą reprezentację modułu.
Aby użyć domyślnej wartości właściwości, należy usunąć właściwość z obiektu konfiguracji.

### <a name="setting-a-property"></a>Ustawianie właściwości

1. W usłudze IoT Hub Znajdź i wybierz urządzenie, którego chcesz zmienić.

1. Kliknij przycisk na urządzeniu, a następnie na **azureiotsecurity** modułu.

1. Kliknij pozycję **bliźniaczą reprezentację modułu tożsamości**.

1. Edytuj żądane właściwości modułu zabezpieczeń.
   
   Na przykład aby skonfigurować zdarzenia połączeń jako wysoki priorytet i zbierania zdarzeń o wysokim priorytecie co 7 minut, należy użyć następującej konfiguracji.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Kliknij pozycję **Zapisz**.

### <a name="using-a-default-value"></a>Przy użyciu wartości domyślnej

Aby użyć domyślnej wartości właściwości, należy usunąć właściwość z obiektu konfiguracji.

## <a name="default-properties"></a>Właściwości domyślne 

Poniższa tabela zawiera właściwości sterowane ASC agentów zabezpieczeń IoT.

Wartości domyślne są dostępne w odpowiednich schemat w [Github](https://aka.ms/iot-security-module-default).

| Name (Nazwa)| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Wymagane: false |Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT7M |Maksymalny czas przed komunikatami o wysokim priorytecie są wysyłane.|
|lowPriorityMessageFrequency |Wymagane: false|Prawidłowe wartości: Czas trwania w ISO 8601 Format |Wartość domyślna: PT5H |Maksymalny czas przed komunikatami o niskim priorytecie są wysyłane.| 
|snapshotFrequency |Wymagane: false|Prawidłowe wartości: czas trwania w ISO 8601 Format |Wartość domyślna PT13H |Interwał tworzenia migawki stanu urządzenia.| 
|maxLocalCacheSizeInBytes |Wymagane: false |Prawidłowe wartości: |Wartość domyślna: 2560000 większy niż 8192 | Pamięci masowej (w bajtach) dozwoloną dla pamięci podręcznej komunikatu o agenta. Maksymalna ilość miejsca do przechowywania komunikatów na urządzeniu, przed wysłaniem wiadomości.| 
|maxMessageSizeInBytes |Wymagane: false |Prawidłowe wartości: Liczba dodatnia większa niż 8192 mniej niż 262144 |Wartość domyślna: 204800 |Maksymalny dozwolony rozmiar równy agenta pod kątem komunikatów w chmurze. To ustawienie steruje maksymalną dane przesyłane w każdej wiadomości. |
|eventPriority${EventName} |Wymagane: false |Prawidłowe wartości: Wysoki, niski wyłączone |Wartości domyślne: |Priorytet każdego agenta wygenerowanych zdarzeń | 

### <a name="supported-security-events"></a>Zdarzenia zabezpieczeń obsługiwane

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
 

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie usługi ASC zaleceń IoT](concept-recommendations.md)
- [Zapoznaj się z usługą ASC alertów IoT](concept-security-alerts.md)
- [Dostęp do danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
