---
title: Konfigurowanie usługi Azure Security Center dla agenta IoT| Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować usługę Azure Security Center dla agentów zabezpieczeń IoT do użytku z usługą Azure Security Center for IoT security service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 70396cdcaf8b6e2ac66619290eea35a7b260cd9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461253"
---
# <a name="tutorial-configure-security-agents"></a>Samouczek: Konfigurowanie agentów zabezpieczeń

W tym artykule opisano usługę Azure Security Center dla agentów zabezpieczeń IoT oraz szczegółowe informacje na temat ich zmiany i konfigurowania. 

> [!div class="checklist"]
> * Konfigurowanie alertów zabezpieczeń
> * Zmienianie zachowania agenta przez edytowanie właściwości bliźniaczej reprezentacji
> * Odnajduj konfigurację domyślną

## <a name="agents"></a>Agenci

Usługa Azure Security Center dla agentów zabezpieczeń IoT zbiera dane z urządzeń IoT i wykonuje akcje zabezpieczeń w celu wyeliminowania wykrytych luk w zabezpieczeniach. Konfiguracja agenta zabezpieczeń jest kontrolowana przy użyciu zestawu właściwości bliźniaczej reprezentacji modułu, które można dostosować. Ogólnie rzecz biorąc, pomocnicze aktualizacje tych właściwości są rzadkie.  

Obiekt konfiguracji bliźniaczej reprezentacji agenta zabezpieczeń usługi Azure Security Center for IoT jest obiektem w formacie JSON. Obiekt konfiguracji to zestaw właściwości, które można zdefiniować w celu kontrolowania zachowania agenta. 

Te konfiguracje ułatwiają dostosowanie agenta dla każdego scenariusza wymagane. Na przykład automatyczne wykluczanie niektórych zdarzeń lub utrzymywanie zużycia energii na minimalnym poziomie jest możliwe przez skonfigurowanie tych właściwości.  

Użyj [schematu](https://aka.ms/iot-security-github-module-schema) konfiguracji agenta zabezpieczeń Usługi Azure Security Center dla IoT, aby wprowadzić zmiany.  

## <a name="configuration-objects"></a>Obiekty konfiguracyjne 

Właściwości związane z każdym agentem zabezpieczeń Usługi Azure Security Center dla IoT znajdują się w obiekcie konfiguracji agenta, w sekcji żądane właściwości modułu **azureiotsecurity.** 

Aby zmodyfikować konfigurację, należy utworzyć i zmodyfikować ten obiekt wewnątrz tożsamości bliźniaczej reprezentacji modułu **azureiotsecurity.** 

Jeśli obiekt konfiguracji agenta nie istnieje w bliźniaczej reprezentacji modułu **azureiotsecurity,** wszystkie wartości właściwości agenta zabezpieczeń są ustawione na domyślne. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Schemat konfiguracji i sprawdzanie poprawności 

Upewnij się, że konfiguracja agenta jest sprawdzana pod kątem tego [schematu](https://aka.ms/iot-security-github-module-schema). Agent nie zostanie uruchomiony, jeśli obiekt konfiguracji nie jest zgodny ze schematem.

 
Jeśli gdy agent jest uruchomiony, obiekt konfiguracji zostanie zmieniony na nieprawidłową konfigurację (konfiguracja nie pasuje do schematu), agent zignoruje nieprawidłową konfigurację i będzie nadal używał bieżącej konfiguracji. 

### <a name="configuration-validation"></a>Sprawdzanie poprawności konfiguracji

Agent zabezpieczeń Usługi Azure Security Center for IoT zgłasza swoją bieżącą konfigurację wewnątrz sekcji zgłoszonych właściwości bliźniaczej tożsamości modułu **azureiotsecurity.**
Agent raportuje wszystkie dostępne właściwości, jeśli właściwość nie została ustawiona przez użytkownika, agent raportuje domyślną konfigurację.

Aby sprawdzić poprawność konfiguracji, porównaj wartości ustawione w żądanej sekcji z wartościami zgłoszonymi w zgłoszonej sekcji.

Jeśli istnieje niezgodność między żądane i zgłaszane właściwości, agent nie był w stanie przeanalizować konfiguracji.

Sprawdź poprawność żądanych właściwości względem [schematu,](https://aka.ms/iot-security-github-module-schema)napraw błędy i ponownie ustaw żądane właściwości!

> [!NOTE]
> Alert o błędzie konfiguracji zostanie zwolniony z agenta w przypadku, gdy agent nie był w stanie przeanalizować żądanej konfiguracji.
> Porównaj zgłoszoną i żądaną sekcję, aby dowiedzieć się, czy alert nadal ma zastosowanie

## <a name="editing-a-property"></a>Edytowanie właściwości 

Wszystkie właściwości niestandardowe muszą być ustawione wewnątrz obiektu konfiguracji agenta w obiekcie **azureiotsecurity** modułu bliźniaczej reprezentacji.
Aby użyć domyślnej wartości właściwości, usuń właściwość z obiektu konfiguracji.

### <a name="setting-a-property"></a>Ustawianie właściwości

1. W centrum IoT Hub znajdź i wybierz urządzenie, które chcesz zmienić.

1. Kliknij na urządzeniu, a następnie na **azureiotsecurity** modułu.

1. Kliknij **moduł Identity Twin**.

1. Edytuj właściwości, które chcesz zmienić w module zabezpieczeń.
   
   Na przykład, aby skonfigurować zdarzenia połączenia jako wysoki priorytet i zbierać zdarzenia o wysokim priorytecie co 7 minut, należy użyć następującej konfiguracji.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Kliknij przycisk **Zapisz**.

### <a name="using-a-default-value"></a>Używanie wartości domyślnej

Aby użyć domyślnej wartości właściwości, usuń właściwość z obiektu konfiguracji.

## <a name="default-properties"></a>Właściwości domyślne 

Poniższa tabela zawiera właściwości kontrolowane usługi Azure Security Center dla agentów zabezpieczeń IoT.

Wartości domyślne są dostępne w odpowiednim schemacie w [usłudze GitHub](https\://aka.ms/iot-security-module-default).

| Nazwa| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|wysokaPriorityMessageFrequency|Wymagane: false |Prawidłowe wartości: Czas trwania w formacie ISO 8601 |Wartość domyślna: PT7M |Maksymalny przedział czasu przed wysłaniem wiadomości o wysokim priorytecie.|
|niskaPriorityMessageFrequency |Wymagane: false|Prawidłowe wartości: Czas trwania w formacie ISO 8601 |Wartość domyślna: PT5H |Maksymalny czas przed wysłaniem wiadomości o niskim priorytecie.| 
|snapshotFrequency |Wymagaj: false|Prawidłowe wartości: Czas trwania w formacie ISO 8601 |Wartość domyślna PT13H |Przedział czasu do tworzenia migawek stanu urządzenia.| 
|maxLocalCacheSizeInBytes |Wymagane: false |Prawidłowe wartości: |Wartość domyślna: 2560000, większa niż 8192 | Maksymalna ilość miejsca do magazynowania (w bajtach) dozwolona dla pamięci podręcznej wiadomości agenta. Maksymalna ilość miejsca do przechowywania wiadomości na urządzeniu przed wysłaniem wiadomości.| 
|maxMessageSizeInBytes |Wymagane: false |Prawidłowe wartości: Liczba dodatnia, większa niż 8192, mniejsza niż 262144 |Wartość domyślna: 204800 |Maksymalny dozwolony rozmiar wiadomości agenta w chmurze. To ustawienie steruje maksymalną ilością danych wysyłanych w każdej wiadomości. |
|eventPriority${Nazwa zdarzenia} |Wymagane: false |Prawidłowe wartości: Wysoki, Niski, Wyłączony |Wartości domyślne: |Priorytet każdego zdarzenia wygenerowanego przez agenta | 

### <a name="supported-security-events"></a>Obsługiwane zdarzenia zabezpieczeń

|Nazwa wydarzenia| PropertyName | Wartość domyślna| Zdarzenie migawki| Szczegóły Stan  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Zdarzenie diagnostyczne|eventPriorityDiagnostic| Wyłączone| False| Zdarzenia diagnostyczne związane z agentem. Użyj tego zdarzenia do szczegółowego rejestrowania.| 
|Błąd konfiguracji |eventPriorityConfigurationError |Małe |False |Agent nie może przeanalizować konfiguracji. Sprawdź konfigurację względem schematu.| 
|Statystyki porzuconych zdarzeń |eventPriorityDroppedEventsStatistics |Małe |True|Statystyki zdarzeń związanych z agentem. |
|Podłączony sprzęt|eventPriorityConnectedHardware |Małe |True |Migawka całego sprzętu podłączonego do urządzenia.|
|Porty nasłuchiwania|eventPriorityListeningPorts |Wysoka |True |Migawka wszystkich otwartych portów nasłuchiwania w urządzeniu.|
|Tworzenie procesu |eventPriorityProcessKrównienie |Małe |False |Inspekcje procesu tworzenia na urządzeniu.|
|Zakończenie procesu|eventPriorityProcessTerminate |Małe |False |Inspekcje zakończenia procesu na urządzeniu.| 
|Informacje o systemie |eventPrioritySystemInformation |Małe |True |Migawka informacji o systemie (na przykład: system operacyjny lub procesora CPU).| 
|Użytkownicy lokalni| eventPriorityLocalUsers |Wysoka |True|Migawka zarejestrowanych użytkowników lokalnych w systemie. |
|Logowanie|  eventPriorityLogin |Wysoka|False|Inspekcja zdarzeń logowania do urządzenia (logowania lokalne i zdalne).|
|Tworzenie połączenia |eventPriorityConnectionCreate|Małe|False|Przeprowadza inspekcje połączeń TCP utworzonych do i z urządzenia. |
|Konfiguracja zapory| eventPriorityFirewallKonfiguracja|Małe|True|Migawka konfiguracji zapory urządzenia (reguły zapory). |
|Punkt odniesienia dla systemu operacyjnego| eventPriorityOSBaseline| Małe|True|Migawka sprawdzania linii bazowej systemu operacyjnego urządzenia.|
|
 

## <a name="next-steps"></a>Następne kroki

- [Poznaj zaleceń dotyczące usługi Azure Security Center dla IoT](concept-recommendations.md)
- [Eksploruj usługi Azure Security Center pod kątem alertów IoT](concept-security-alerts.md)
- [Dostęp do nieprzetworzonych danych zabezpieczeń](how-to-security-data-access.md)
