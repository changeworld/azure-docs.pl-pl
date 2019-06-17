---
title: Uzyskiwanie dostępu do danych przy użyciu Centrum zabezpieczeń Azure dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak uzyskać dostęp do danych alertów i zalecenia zabezpieczeń po przy użyciu Centrum zabezpieczeń Azure dla IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200557"
---
# <a name="access-your-security-data"></a>Dostęp do danych zabezpieczeń 

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Security Center (ASC) dla IoT przechowuje alertów zabezpieczeń, zaleceń i danych pierwotnych zabezpieczeń (Jeśli użytkownik chce zapisać ją) w obszarze roboczym usługi Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Aby skonfigurować, który obszar roboczy usługi Log Analytics jest używany:

1. Otwórz Centrum IoT hub.
1. Kliknij przycisk **zabezpieczeń**
2. Kliknij przycisk **ustawienia**i zmień konfigurację obszaru roboczego usługi Log Analytics.

Dostęp do obszaru roboczego usługi Log Analytics po konfiguracji:

1. Wybierz alert lub zalecenia w ASC dla IoT. 
2. Kliknij przycisk **dalsze badanie**, następnie kliknij przycisk **urządzeń, które mają ten alert, kliknij tutaj, aby wyświetlić kolumnę DeviceId**.

Aby uzyskać więcej informacji o wysyłaniu zapytań do danych z usługi Log Analytics, zobacz [wprowadzenie do zapytań w usłudze Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty zabezpieczeń są przechowywane w _AzureSecurityOfThings.SecurityAlert_ tabeli w obszarze roboczym usługi Log Analytics skonfigurowane dla usługi ASC dla rozwiązania IoT.

Podana liczba przydatne zapytania, aby pomóc Ci rozpocząć pracę, eksplorowanie alerty zabezpieczeń zostały wykonane następujące kroki.

### <a name="sample-records"></a>Przykładowe rekordów

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nazwa wyświetlana                           | Opis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Atak siłowy powiodło się.           | Atak metodą ataku siłowego Wymuś na urządzeniu zakończyło się powodzeniem        |    {"Pełną Address": "[\"10.165.12.18:\"]", "Nazwy użytkownika": "[\"\"]", "DeviceId": "IoT urządzenia — Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | Wykryto pomyślne lokalne logowanie do urządzenia     | {"Zdalnego Address": "?", "Port zdalny": "", "Portu lokalnego": "", "Login powłoka": "/ bin/su", "Identyfikator procesu logowania": "28207", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT urządzenia — Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Próba logowania lokalnego na urządzeniu nie powiodła się  | Została wykryta próba logowania lokalnego nie powiodło się z urządzeniem |  {"Zdalnego Address": "?", "Port zdalny": "", "Portu lokalnego": "", "Login powłoka": "/ bin/su", "Identyfikator procesu logowania": "22644", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT urządzenia — Linux"} |

### <a name="device-summary"></a>Podsumowania urządzenia

Wybierz liczbę alertów zabezpieczeń distinct wykryty ostatni tydzień przez Centrum IoT Hub, urządzenia, ważność alertu, typu alertu.

```
// Select number of distinct security alerts detected last week by 
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

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nazwa wyświetlana                           | Licznik |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Atak siłowy powiodło się.           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni        | Próba logowania lokalnego na urządzeniu nie powiodła się  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>Podsumowanie usługi IoT hub

Wybierz liczbę różnych urządzeń, które miały alertów w ostatnim tygodniu, przez Centrum IoT Hub, ważność alertu, typ alertu

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

| IoTHubId                                                                                                       | AlertSeverity | Nazwa wyświetlana                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Atak siłowy powiodło się.           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Średni        | Próba logowania lokalnego na urządzeniu nie powiodła się  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Średni        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Zalecenia dotyczące zabezpieczeń są przechowywane w _AzureSecurityOfThings.SecurityRecommendation_ tabeli w obszarze roboczym usługi Log Analytics skonfigurowane dla usługi ASC dla rozwiązania IoT.

Udostępniliśmy szereg przydatne zapytania, aby ułatwić rozpoczęcie eksplorowania zaleceń dotyczących zabezpieczeń.

### <a name="sample-records"></a>Przykładowe rekordów

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
    
| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Opis | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni | Aktywne | Znaleziono reguły zapory ograniczające w łańcuchu danych wejściowych | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP i portów | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni | Aktywne | Znaleziono reguły zapory ograniczające w łańcuchu danych wejściowych | Reguła zapory znaleziono zawierający warunki dotyczące wzorca dla szerokiego zakresu adresów IP i portów | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Podsumowania urządzenia

Wybierz liczbę zalecenia dotyczące zabezpieczeń active distinct przez Centrum IoT Hub, urządzenia, zalecenie ważności i typu.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Licznik |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Średni        | 4   |


## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj ASC, aby uzyskać IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Poznawanie i eksplorowanie [ASC alertów IoT](concept-security-alerts.md)
- Poznawanie i eksplorowanie [ASC zalecenie IoT](concept-recommendations.md)