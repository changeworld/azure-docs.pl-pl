---
title: Uzyskiwanie dostępu do danych przy użyciu usługi ASC IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tym, jak uzyskać dostęp do danych alertów i zalecenia zabezpieczeń po IoT przy użyciu usługi ASC.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541995"
---
# <a name="access-your-security-data"></a>Dostęp do danych zabezpieczeń 

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC IoT przechowuje alertów zabezpieczeń, zaleceń i danych pierwotnych zabezpieczeń (Jeśli użytkownik chce zapisać ją) w obszarze roboczym usługi Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Aby skonfigurować, który obszar roboczy usługi Log Analytics jest używany:

1. Otwórz Centrum IoT hub.
1. Kliknij przycisk **zabezpieczeń**
2. Kliknij przycisk **ustawienia**i zmień konfigurację obszaru roboczego usługi Log Analytics.

Dostęp do obszaru roboczego usługi Log Analytics po konfiguracji:

1. Wybierz alert w ASC dla IoT. 
2. Kliknij przycisk **dalsze badanie**, następnie kliknij przycisk **urządzeń, które mają ten alert, kliknij tutaj, aby wyświetlić kolumnę DeviceId**.

Aby uzyskać więcej informacji o wysyłaniu zapytań do danych z usługi Log Analytics, zobacz [wprowadzenie do zapytań w usłudze Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Alerty zabezpieczeń

Alerty zabezpieczeń są przechowywane w **ASCforIoT.SecurityAlert** tabeli w ramach skonfigurowanego obszaru roboczego usługi Log Analytics.

Następujące zapytania podstawowego kql umożliwia rozpoczęcie eksplorowania alertów zabezpieczeń.

### <a name="sample-records-query"></a>Sample records query

Aby zaznaczyć kilka rekordów w losowo wybranym momencie: 

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

#### <a name="sample-query-results"></a>Przykładowe wyniki zapytania 

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | Nazwa wyświetlana                           | Opis                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Atak siłowy powiodło się.           | Atak metodą ataku siłowego Wymuś na urządzeniu zakończyło się powodzeniem        |    {"Pełną Address": "[\"10.165.12.18:\"]", "Nazwy użytkownika": "[\"\"]", "DeviceId": "IoT urządzenia — Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | Wykryto pomyślne lokalne logowanie do urządzenia     | {"Zdalnego Address": "?", "Port zdalny": "", "Portu lokalnego": "", "Login powłoka": "/ bin/su", "Identyfikator procesu logowania": "28207", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT urządzenia — Linux"} |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Próba logowania lokalnego na urządzeniu nie powiodła się  | Została wykryta próba logowania lokalnego nie powiodło się z urządzeniem |  {"Zdalnego Address": "?", "Port zdalny": "", "Portu lokalnego": "", "Login powłoka": "/ bin/su", "Identyfikator procesu logowania": "22644", "Nazwa użytkownika": "osoba atakująca", "DeviceId": "IoT urządzenia — Linux"} |

### <a name="device-summary-query"></a>Podsumowanie zapytania urządzeń

Użyj tego zapytania kql, aby wybrać wiele alertów zabezpieczeń z różnych wykryty ostatni tydzień przez usługę IoT Hub, urządzenia, ważność alertu, typu alertu.

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

#### <a name="device-summary-query-results"></a>Wyniki zapytania podsumowania urządzenia

| IoTHubId | DeviceId| AlertSeverity| Nazwa wyświetlana | Licznik |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Atak siłowy powiodło się.           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Próba logowania lokalnego na urządzeniu nie powiodła się  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Crypto Coin Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>Podsumowanie usługi IoT Hub

Skorzystaj z tej kwerendy kql, aby wybrać wiele różnych urządzeń, które miały alertów w ostatnim tygodniu przez Centrum IoT hub, ważność alertu, typ alertu:

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

#### <a name="iot-hub-summary-query-results"></a>Wyniki zapytania podsumowania usługi IoT Hub

| IoTHubId                                                                                                       | AlertSeverity | Nazwa wyświetlana                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Atak siłowy powiodło się.           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Próba logowania lokalnego na urządzeniu nie powiodła się  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Wysoka          | Pomyślne logowanie lokalne na urządzeniu      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Crypto Coin Miner                     | 1          |



## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj ASC, aby uzyskać IoT [— omówienie](overview.md)
- Dowiedz się więcej o ASC IoT [architektury](architecture.md)
- Poznawanie i eksplorowanie [ASC alertów IoT](concept-security-alerts.md)
