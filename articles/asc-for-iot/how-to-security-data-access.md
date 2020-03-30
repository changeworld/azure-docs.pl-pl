---
title: Uzyskiwanie dostępu do danych przy użyciu usługi Azure Security Center dla usługi IoT| Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać dostęp do alertów zabezpieczeń i danych rekomendacji podczas korzystania z usługi Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597184"
---
# <a name="access-your-security-data"></a>Uzyskiwanie dostępu do danych zabezpieczających 

Usługa Azure Security Center for IoT przechowuje alerty zabezpieczeń, zalecenia i surowe dane zabezpieczeń (jeśli zdecydujesz się je zapisać) w obszarze roboczym usługi Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Aby skonfigurować używany obszar roboczy usługi Log Analytics:

1. Otwórz centrum IoT.
1. Kliknij **blok Przegląd** w sekcji **Zabezpieczenia**
2. Kliknij **pozycję Ustawienia**i zmień konfigurację obszaru roboczego usługi Log Analytics.

Aby uzyskać dostęp do alertów i zaleceń w obszarze roboczym usługi Log Analytics po zakończeniu konfiguracji:

1. Wybierz alert lub zalecenie w usłudze Azure Security Center dla IoT. 
2. Kliknij **dalsze badania**, a następnie kliknij, aby **zobaczyć, które urządzenia mają ten alert kliknij tutaj i wyświetl kolumnę DeviceId**.

Aby uzyskać szczegółowe informacje na temat wykonywania zapytań z usługi Log Analytics, zobacz [Wprowadzenie do zapytań w usłudze Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings.SecurityAlert_ w obszarze roboczym usługi Log Analytics skonfigurowanym dla rozwiązania Usługi Azure Security Center dla IoT.

Udostępniliśmy szereg przydatnych zapytań ułatwiających rozpoczęcie eksplorowania alertów zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityAlert
| project 
    TimeGenerated, 
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity, 
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | Identyfikator IoTHubId                                                                                                       | DeviceId      | AlertEverity (Zawsze alerty) | DisplayName                           | Opis                                             | Extendedproperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Brutalny atak siłowy zakończył się sukcesem           | Brutalny atak siłowy na urządzenie zakończył się sukcesem        |    { "Pełny adres\"źródłowy": "[ 10.165.12.18:\"]",\"\""Nazwy użytkowników": "[ ]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | Wykryto pomyślne lokalne logowanie do urządzenia     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Nieudana próba logowania lokalnego na urządzeniu  | Wykryto nieudaną próbę logowania lokalnego do urządzenia |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Podsumowanie urządzenia

Pobierz liczbę różnych alertów zabezpieczeń wykrytych w ostatnim tygodniu, pogrupowane według Usługi IoT Hub, urządzenia, ważności alertu, typu alertu.

```
// Get the number of distinct security alerts detected in the last week, grouped by 
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId, 
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| Identyfikator IoTHubId                                                                                                       | DeviceId      | AlertEverity (Zawsze alerty) | DisplayName                           | Liczba |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Brutalny atak siłowy zakończył się sukcesem           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Nieudana próba logowania lokalnego na urządzeniu  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Crypto Moneta Górnik                     | 4   |

### <a name="iot-hub-summary"></a>Podsumowanie centrum IoT

Wybierz kilka różnych urządzeń, które miały alerty w ostatnim tygodniu, przez IoT Hub, ważność alertu, typ alertu

```
// Select number of distinct devices which had alerts in the last week, by 
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId, 
    AlertSeverity,
    DisplayName
```

| Identyfikator IoTHubId                                                                                                       | AlertEverity (Zawsze alerty) | DisplayName                           | CntDevices (CntDevices) |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Brutalny atak siłowy zakończył się sukcesem           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Nieudana próba logowania lokalnego na urządzeniu  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Crypto Moneta Górnik                     | 1          |

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Zalecenia dotyczące zabezpieczeń są przechowywane w tabeli _AzureSecurityOfThings.SecurityRecommendation_ w obszarze roboczym usługi Log Analytics skonfigurowanym dla rozwiązania Usługi Azure Security Center dla IoT.

Udostępniliśmy szereg przydatnych zapytań, które pomogą Ci rozpocząć eksplorowanie zaleceń dotyczących zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordy

Wybierz kilka losowych rekordów

```
// Select a few random records
//
SecurityRecommendation
| project 
    TimeGenerated, 
    IoTHubId=AssessedResourceId, 
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```
    
| TimeGenerated | Identyfikator IoTHubId | DeviceId | RekomendacjeEverity | Stan rekomendacji | Nazwa rekomendacjiWyświetlajna | Opis | ZalecenieDodatkdaty |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | Aktywne | Znaleziono zasadę zapory permisywnej w łańcuchu wprowadzania | Znaleziono regułę w zaporze, która zawiera wzorzec dopuszczalny dla szerokiego zakresu adresów IP lub portów | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort :\"\"\",\"DestinationAddress : , DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | Aktywne | Znaleziono zasadę zapory permisywnej w łańcuchu wprowadzania | Znaleziono regułę w zaporze, która zawiera wzorzec dopuszczalny dla szerokiego zakresu adresów IP lub portów | {"Rules":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort :\"\"\",\"DestinationAddress : , DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Podsumowanie urządzenia

Uzyskaj liczbę różnych aktywnych zaleceń dotyczących zabezpieczeń, pogrupowanych według usługi IoT Hub, urządzenia, ważności rekomendacji i typu.

```
// Get the number of distinct active security recommendations, grouped by by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| Identyfikator IoTHubId                                                                                                       | DeviceId      | RekomendacjeEverity | Liczba |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 4   |


## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie](overview.md) usługi Azure Security Center dla IoT
- Dowiedz się więcej o usłudze Azure Security Center dla [architektury](architecture.md) IoT
- Opis i [eksplorowanie usługi Azure Security Center dla alertów IoT](concept-security-alerts.md)
- Opis i eksplorowanie [usługi Azure Security Center w celu uzyskania rekomendacji IoT](concept-recommendations.md)
